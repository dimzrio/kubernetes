## Service ##

#### Get services ####
~~~~
$ kubectl get svc

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.233.0.1   <none>        443/TCP   5d
~~~~


#### Create service ClusterIP ####
This is the default Service Type, only reachable from within the cluster. Consider this as an internal load balancer.
You need kube-proxy if you want accessing service.

![ClusterIP](https://1.bp.blogspot.com/-dXszbTZ3eB4/XL6o8epTy9I/AAAAAAAADPg/BLo1uJtzY_MPcL6YhWg426MRU05sjQx8QCLcBGAs/s1600/clusterip.jpeg)

~~~~
$ kubectl apply -f cluster-ip/nginx-clusterip-svc.yaml
replicaset.apps/webserver-rs created
service/webserver-clusterip-services created
~~~~

~~~~
$ kubectl describe -n applications svc webserver-clusterip-services
Name:              webserver-clusterip-services
Namespace:         applications
Labels:            <none>
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"webserver-clusterip-services","namespace":"applications"},"spec":...
Selector:          app=nginx-services
Type:              ClusterIP
IP:                10.245.12.89
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         10.244.1.173:80
Session Affinity:  None
Events:            <none>
~~~~

~~~~
$ kubectl exec -it -n applications webserver-rs-7hh6q sh

/# curl http://10.245.12.89
....
<h1>Welcome to nginx!</h1>
....
~~~~

#### Create service nodePort ####
A NodePort is an open port (30000–32767) on every node of your cluster. Kubernetes transparently routes incoming traffic on the NodePort to your service, even if your application is running on a different node.

![NodePort](https://1.bp.blogspot.com/-iOoMWu1gJgw/XL6o8jtoK9I/AAAAAAAADPk/pL8zlEZ1dT0PolPUipPF-sbpF3FS2QW5QCLcBGAs/s1600/nodeport.jpeg)

~~~~
$ kubectl expose rc webserver --port=8888 --target-port=80 --type='NodePort' --namespace=webapps --name=web-service-nodeport
service/web-service-nodeport exposed
~~~~
~~~~
$ kubectl apply -f service/yaml/svc-nodeport.yaml
service/web-service-nodeport created

$ kubectl describe svc web-service-nodeport --namespace=webapps
Name:                     web-service-nodeport
Namespace:                webapps
Labels:                   <none>
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"web-service-nodeport","namespace":"webapps"},"spec":{"ports":[{"n...
Selector:                 app=web
Type:                     NodePort
IP:                       10.233.20.47
Port:                     http  8888/TCP
TargetPort:               80/TCP
NodePort:                 http  30080/TCP
Endpoints:                10.233.102.5:80,10.233.85.1:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

URL: http://<ip-server-host>:30080
~~~~


#### Create service load balancer ####
Using a LoadBalancer service type automatically deploys an external load balance.

![LoadBalancer](https://4.bp.blogspot.com/-gMlVttdhnlE/XL6o8fLZAvI/AAAAAAAADPc/hhOYR4BYM-cuz7nfO1W0q3grHPYXO6RMwCLcBGAs/s1600/lb.jpeg)

~~~~
$ kubectl apply -f service/yaml/svc-loadbalancer.yaml
service/web-service-loadbalance created

$ kubectl describe svc web-service-loadbalance --namespace=webapps
Name:                     web-service-loadbalance
Namespace:                webapps
Labels:                   <none>
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"web-service-loadbalance","namespace":"webapps"},"spec":{"external...
Selector:                 app=nginx-web-apps
Type:                     LoadBalancer
IP:                       10.233.18.154
External IPs:             188.166.207.187
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30080/TCP
Endpoints:                10.233.102.4:80,10.233.85.2:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

Note:
Need forwarding rule from balancer 188.166.207.187:80 to <ipworker>:30080

URL: http://188.166.207.187
~~~~

#### Create service ingress ####

![Ingress](https://4.bp.blogspot.com/-2ToTggM7hm4/XL6o9WJ-2QI/AAAAAAAADPw/St12QMgoT8o3kwATpMLUAqQJl3Em0L-ggCEwYBhgL/s1600/nodeport.png)

Install nginx ingress
~~~~
$ kubectl apply -f common/ns-and-sa.yaml
$ kubectl apply -f common/default-server-secret.yaml
$ kubectl apply -f common/nginx-config.yaml
$ kubectl apply -f rbac/rbac.yaml
$ kubectl apply -f daemon-set/nginx-ingress.yaml

Refer: https://github.com/nginxinc/kubernetes-ingress/blob/master/docs/installation.md
~~~~
~~~~
$ kubectl apply -f service/yaml/svc-ingress.yaml -n nginx-ingress
deployment.apps "nginx-deploy" created
deployment.apps "tomcat-deploy" created
service "nginx-service" created
service "tomcat-service" created
ingress.extensions "web-service-ingress" created
~~~~
~~~~
$ kubectl get all -n nginx-ingress
NAME                                 READY     STATUS    RESTARTS   AGE
pod/nginx-deploy-5c49f45c4f-npqfh    1/1       Running   0          6s
pod/nginx-deploy-5c49f45c4f-pq52n    1/1       Running   0          6s
pod/nginx-ingress-755df5c4cc-8bhgz   1/1       Running   0          8m
pod/tomcat-deploy-864686dcd7-pqh2m   1/1       Running   0          6s
pod/tomcat-deploy-864686dcd7-q7fhz   1/1       Running   0          6s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/nginx-service    ClusterIP   10.233.24.73    <none>        80/TCP    6s
service/tomcat-service   ClusterIP   10.233.23.225   <none>        80/TCP    6s

NAME                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deploy    2         2         2            2           6s
deployment.apps/nginx-ingress   1         1         1            1           8m
deployment.apps/tomcat-deploy   2         2         2            2           6s

NAME                                       DESIRED   CURRENT   READY     AGE
replicaset.apps/nginx-deploy-5c49f45c4f    2         2         2         6s
replicaset.apps/nginx-ingress-755df5c4cc   1         1         1         8m
replicaset.apps/tomcat-deploy-864686dcd7   2         2         2         6s
~~~~

Expose to balancer
~~~~
$ kubectl expose deploy nginx-ingress --name=nginx-ingress-webserver --protocol=TCP --target-port=80 --external-ip=x.x.x.x --type=LoadBalancer -n nginx-ingress
service "nginx-ingress-webserver" exposed
~~~~