## With Helm
1. https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/
2. Manually update Service and add `externalIPs`. Running on VirtualBox,...my load balancer is my machine.
```
  type: LoadBalancer
  externalIPs:
    - 192.168.0.254
```

## By Hand
https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-manifests/

### This killed me
- https://www.vagrantup.com/docs/networking/public_network.html#default-router

https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal
https://www.digitalocean.com/community/tutorials/iptables-essentials-common-firewall-rules-and-commands#service-web-server