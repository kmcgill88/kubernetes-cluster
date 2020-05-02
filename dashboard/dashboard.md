## Admin Token
- `watson -n kubernetes-dashboard describe secret $(watson -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')`

https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui
https://stackoverflow.com/questions/52893111/no-endpoints-available-for-service-kubernetes-dashboard
