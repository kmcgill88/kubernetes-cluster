https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui

watson apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml

watson -n kubernetes-dashboard describe secret $(watson -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

https://stackoverflow.com/questions/52893111/no-endpoints-available-for-service-kubernetes-dashboard


vagrant@k8s-node-2:~$ curl  https://192.168.0.254:6443/version?timeout=32s
curl: (60) server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
More details here: http://curl.haxx.se/docs/sslcerts.html

curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.
vagrant@k8s-node-2:~$ 




2 Starting overwatch
2020/04/26 06:46:52 Using apiserver-host location: https://192.168.0.254:6443
2020/04/26 06:46:52 Using namespace: kubernetes-dashboard
2020/04/26 06:46:52 Skipping in-cluster config
2020/04/26 06:46:52 Using random key for csrf signing
2020/04/26 06:47:22 Error while initializing connection to Kubernetes apiserver. This most likely means that the cluster is misconfigured (e.g., it has invalid apiserver certificates or service account's configuration) or the --apiserver-host param points to a server that does not exist. Reason: Get https://192.168.0.254:6443/version?timeout=32s: dial tcp 192.168.0.254:6443: i/o timeout
Refer to our FAQ and wiki pages for more information: https://github.com/kubernetes/dashboard/wiki/FAQ
vagrant@k8s-node-1:~$ curl  https://192.168.0.254:6443/version?timeout=32s
{
  "major": "1",
  "minor": "18",
  "gitVersion": "v1.18.2",
  "gitCommit": "52c56ce7a8272c798dbc29846288d7cd9fbae032",
  "gitTreeState": "clean",
  "buildDate": "2020-04-16T11:48:36Z",
  "goVersion": "go1.13.9",
  "compiler": "gc",
  "platform": "linux/amd64"
}vagrant@k8s-node-1:~$ 




2020/04/26 06:03:56 Using namespace: kubernetes-dashboard
2020/04/26 06:03:56 Using in-cluster config to connect to apiserver
2020/04/26 06:03:56 Starting overwatch
2020/04/26 06:03:56 Using secret token for csrf signing
2020/04/26 06:03:56 Initializing csrf token from kubernetes-dashboard-csrf secret
panic: Get https://10.96.0.1:443/api/v1/namespaces/kubernetes-dashboard/secrets/kubernetes-dashboard-csrf: dial tcp 10.96.0.1:443: i/o timeout

goroutine 1 [running]:
github.com/kubernetes/dashboard/src/app/backend/client/csrf.(*csrfTokenManager).init(0xc0004c1540)
	/home/travis/build/kubernetes/dashboard/src/app/backend/client/csrf/manager.go:40 +0x3b4
github.com/kubernetes/dashboard/src/app/backend/client/csrf.NewCsrfTokenManager(...)
	/home/travis/build/kubernetes/dashboard/src/app/backend/client/csrf/manager.go:65
github.com/kubernetes/dashboard/src/app/backend/client.(*clientManager).initCSRFKey(0xc00048c080)
	/home/travis/build/kubernetes/dashboard/src/app/backend/client/manager.go:494 +0xc7
github.com/kubernetes/dashboard/src/app/backend/client.(*clientManager).init(0xc00048c080)
	/home/travis/build/kubernetes/dashboard/src/app/backend/client/manager.go:462 +0x47
github.com/kubernetes/dashboard/src/app/backend/client.NewClientManager(...)
	/home/travis/build/kubernetes/dashboard/src/app/backend/client/manager.go:543
main.main()
	/home/travis/build/kubernetes/dashboard/src/app/backend/dashboard.go:105 +0x212




