# Network Policies #

When we need network pilicies?
- Namespaces help us to organize resources
- Provide isolation namespaces from another namespaces
- Filtering service/pod from another pod/service, by default every pod/service accept traffic from everyone.

A network policy can provice:
- A list of ingress rules indicating which inbound traffic is allowed.
- A list of egress rules indicating which outbound traffic is allowed.
- A pod can be "selected" by any number of network policies.

### Scenario 1
Block connection from another pod, allow connection from specifiec pods.

Run a web server in a pod.
~~~~
$ kubectl create deployment nginx --image=nginx
deployment.apps/nginx created
~~~~

~~~~
$ kubectl get pods -o wide --show-labels
NAME                     READY   STATUS    RESTARTS   AGE     IP              NODE          NOMINATED NODE   READINESS GATES   LABELS
nginx-65f88748fd-ppvpr   1/1     Running   0          3m54s   10.233.65.141   k8s-worker2   <none>           <none>            app=nginx,pod-template-hash=65f88748fd
~~~~

Test accessing nginx from master
~~~~
$ curl 10.233.65.141
....
<h1>Welcome to nginx!</h1>
....
~~~~

Create a network policy to block all access to the web server.
~~~~
$ kubectl apply -f scenario-1/netpol-deny.yaml
networkpolicy.networking.k8s.io/deny-all created
~~~~

~~~~
$ kubectl get netpol
NAME       POD-SELECTOR   AGE
deny-all   app=nginx        35s
~~~~

NOTE: After deploy network policy, the curl command should now timeout.

Create another network policy to allow access only from specific pods.
~~~~
$ kubectl apply -f netpol-allow.yaml
networkpolicy.networking.k8s.io/allow-curl created
~~~~

Testing curl
~~~~
$ kubectl run -i --tty --rm curl --image=centos --restart=Never -- bash
If you don't see a command prompt, try pressing enter.
[root@curl /]# curl 10.233.65.141
....
<h1>Welcome to nginx!</h1>
....
~~~~

### Scenario 2
Block connection from another namepsaces, and allow specifiec namespaces.

Create NS application
~~~~
$ kubectl create ns applications
namespace/applications created
~~~~

Deploy nginx to ns applications
~~~~
$ kubectl create deployment nginx --image=nginx --namespace=applications
deployment.apps/nginx created
~~~~

Drop connection from another namespace to applications.
~~~~
$ kubectl apply -f scenario-2/netpol-deny.yaml
networkpolicy.networking.k8s.io/deny-all created
~~~~

Run alpine in default namespace
~~~~
$ kubectl run -i --tty --rm curl --image=centos --restart=Never -- bash
If you don't see a command prompt, try pressing enter.

[root@curl /]# curl 10.233.73.109

~~~~
Note: The curl command should be timeout.

Allowing connection from namespace testing
~~~~
$ kubectl apply -f scenario-2/netpol-allow.yaml
networkpolicy.networking.k8s.io/allow-testing-ns created
~~~~

~~~~
$ kubectl create ns testing
namespace/testing created
~~~~ 

for example i will set label name=testing for namespace testing.
~~~~
$ kubectl label ns testing name=testing
namespace/testing labeled
~~~~
~~~~
$ kubectl get ns --show-labels
....
NAME              STATUS   AGE     LABELS
applications      Active   21m     <none>
testing           Active   6m48s   name=testing,ns=testing
....
~~~~

Testing curl from ns testing
~~~~
$ kubectl run -i --tty --rm curl --image=centos --namespace=testing --restart=Never -- bash
If you don't see a command prompt, try pressing enter.

[root@curl /]# curl 10.233.73.109
...
<h1>Welcome to nginx!</h1>
...
~~~~