# -*- mode: ruby -*-
# vi: set ft=ruby :

servers = [
    {
        :name => "k8s-master",
        :type => "master",
        :mac => "744C14006844",
        :box => "ubuntu/xenial64",
        :box_version => "20200415.0.0",
        :eth1 => "192.168.0.254",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-1",
        :type => "node",
        :mac => "CCF5529B70C4",
        :box => "ubuntu/xenial64",
        :box_version => "20200415.0.0",
        :eth1 => "192.168.0.253",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-2",
        :type => "node",
        :mac => "C074F9FC7AEC",
        :box => "ubuntu/xenial64",
        :box_version => "20200415.0.0",
        :eth1 => "192.168.0.252",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-3",
        :type => "node",
        :mac => "AA0BEE86CEC8",
        :box => "ubuntu/xenial64",
        :box_version => "20200415.0.0",
        :eth1 => "192.168.0.251",
        :mem => "2048",
        :cpu => "2"
    }
]

# This script to install k8s using kubeadm will get executed after a box is provisioned
$configureBox = <<-SCRIPT

    # install docker
    apt-get update
    apt-get install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
    apt-get update && apt-get install -y docker-ce

    # run docker commands as vagrant user (sudo not required)
    usermod -aG docker vagrant

    # install kubeadm
    apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
    apt-get update
    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

    # kubelet requires swap off
    swapoff -a

    # keep swap off after reboot
    sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`
    # set node-ip
    sudo sed -i "/^[^#]*KUBELET_EXTRA_ARGS=/c\KUBELET_EXTRA_ARGS=--node-ip=$IP_ADDR" /etc/default/kubelet
    sudo systemctl restart kubelet
SCRIPT

$configureMaster = <<-SCRIPT
    echo "This is master"
    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`

    # install k8s master
    HOST_NAME=$(hostname -s)
    kubeadm init --apiserver-advertise-address=$IP_ADDR --apiserver-cert-extra-sans=$IP_ADDR  --node-name $HOST_NAME --pod-network-cidr=10.244.0.0/16

    #copying credentials to regular user - vagrant
    sudo --user=vagrant mkdir -p /home/vagrant/.kube
    cp -i /etc/kubernetes/admin.conf /home/vagrant/.kube/config
    chown $(id -u vagrant):$(id -g vagrant) /home/vagrant/.kube/config

    export KUBECONFIG=/etc/kubernetes/admin.conf

    # install flannel pod network addon
    # https://medium.com/@ErrInDam/taming-kubernetes-for-fun-and-profit-60a1d7b353de
    kubectl apply -f https://raw.githubusercontent.com/kmcgill88/kubernetes-cluster/public-network/flannel/flannel.yaml

    kubeadm token create --print-join-command >> /etc/kubeadm_join_cmd.sh
    chmod +x /etc/kubeadm_join_cmd.sh

    # required for setting up password less ssh between guest VMs
    sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config
    sudo service sshd restart

    # Install dashboard
    kubectl apply -f https://raw.githubusercontent.com/kmcgill88/kubernetes-cluster/public-network/dashboard/dashboard.yaml
    kubectl apply -f https://raw.githubusercontent.com/kmcgill88/kubernetes-cluster/public-network/dashboard/dashboard-adminuser.yaml

SCRIPT

$configureNode = <<-SCRIPT
    echo "This is worker"
    apt-get install -y sshpass
    sshpass -p "vagrant" scp -o StrictHostKeyChecking=no vagrant@192.168.0.254:/etc/kubeadm_join_cmd.sh .

    # copy master ca cert and install it
    sshpass -p "vagrant" scp -o StrictHostKeyChecking=no vagrant@192.168.0.254:/etc/kubernetes/pki/ca.crt .
    sudo mkdir /usr/local/share/ca-certificates/k8/
    sudo cp ca.crt /usr/local/share/ca-certificates/k8/
    sudo update-ca-certificates
    sh ./kubeadm_join_cmd.sh
SCRIPT

Vagrant.configure("2") do |config|

    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]

            # https://stackoverflow.com/questions/12538162/setting-a-vms-mac-address-in-vagrant
            config.vm.network "public_network", bridge: 'enp27s0', mac: opts[:mac]

            config.vm.provider "virtualbox" do |v|
                v.name = opts[:name]
                v.customize ["modifyvm", :id, "--groups", "/McGillDevTech"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
                v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
            end

            # we cannot use this because we can't install the docker version we want - https://github.com/hashicorp/vagrant/issues/4871
            #config.vm.provision "docker"

            config.vm.provision "shell", inline: $configureBox

            if opts[:type] == "master"
                config.vm.provision "shell", inline: $configureMaster
            else
                config.vm.provision "shell", inline: $configureNode
            end

        end

    end

end 