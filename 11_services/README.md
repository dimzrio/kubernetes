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
$ kubectl apply -f clusterip/nginx-clusterip-svc.yaml
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
$ kubectl expose rc webserver-nodeport-services --port=80 --target-port=80 --type='NodePort' --namespace=webapps --name=web-service-nodeport
service/web-service-nodeport exposed
~~~~

~~~~
$ kubectl apply -f nodeport/nginx-nodeport-svc.yaml
replicaset.apps/webserver-rs created
service/webserver-nodeport-services created

$ kubectl describe service/webserver-nodeport-services -n applications
Name:                     webserver-nodeport-services
Namespace:                applications
Labels:                   <none>
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"webserver-nodeport-services","namespace":"applications"},"spec":{...
Selector:                 app=nginx-services
Type:                     NodePort
IP:                       10.245.177.4
Port:                     http  80/TCP
TargetPort:               80/TCP
NodePort:                 http  30080/TCP
Endpoints:                10.244.1.28:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
~~~~

~~~~
$ curl http://<ip-public-worker>:30080
....
<h1>Welcome to nginx!</h1>
....
~~~~

#### Create service load balancer ####
Using a LoadBalancer service type automatically deploys an external load balance.
Kubernetes service will be getting ip from cloud provider load balancer as automatically.
In this example, i'm using digitalocean load balancer service.

![LoadBalancer](https://4.bp.blogspot.com/-gMlVttdhnlE/XL6o8fLZAvI/AAAAAAAADPc/hhOYR4BYM-cuz7nfO1W0q3grHPYXO6RMwCLcBGAs/s1600/lb.jpeg)

~~~~
$ kubectl apply -f loadbalancer/nginx-loadbalance-svc.yaml
replicaset.apps/webserver-rs created
service/webserver-loadbalance-services created
~~~~

~~~~
$ kubectl describe svc web-service-loadbalance --namespace=webapps
Name:                     webserver-loadbalance-services
Namespace:                applications
Labels:                   <none>
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"webserver-loadbalance-services","namespace":"applications"},"spec...
Selector:                 app=nginx-services
Type:                     LoadBalancer
IP:                       10.245.45.92
LoadBalancer Ingress:     139.59.194.95 <------ Load balance from cloud provider
Port:                     http  80/TCP
TargetPort:               80/TCP
NodePort:                 http  31102/TCP
Endpoints:                10.244.1.208:80,10.244.2.27:80
Session Affinity:         None
External Traffic Policy:  Cluster
~~~~

~~~~
$ curl http://139.59.194.95
....
<h1>Welcome to nginx!</h1>
....
~~~~