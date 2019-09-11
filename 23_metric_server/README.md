# Horizontal POD Autocaler

Create hpa for deployment nginx-deploy
~~~~
$ kubectl autoscale deploy nginx-deploy --cpu-percent=80 --max=8 --min=2 -n applications
horizontalpodautoscaler.autoscaling/nginx-deploy autoscaled
~~~~

Check hpa
~~~~
$ kubectl get hpa
NAME         REFERENCE               TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   <unknown>/10%   1         5         0          13h
~~~~

### Fix unkown target

Download metrics-server
~~~~
$ git clone https://github.com/kubernetes-incubator/metrics-server.git
$ cd metrics-server
$ nano metrics-server/deploy/1.8+/metrics-server-deployment.yaml
    containers:
        - name: metrics-server
            image: k8s.gcr.io/metrics-server-amd64:v0.3.1
    command:
        - /metrics-server
        - --kubelet-insecure-tls
        - --kubelet-preferred-address-types=InternalIP
~~~~

Deploy all yaml file
~~~~
$ kubectl apply -f metrics-server/deploy/1.8+/.
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
serviceaccount/metrics-server created
deployment.apps/metrics-server created
service/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
~~~~

Get metric server
~~~~
$ kubectl get pods -n kube-system
kube-scheduler-centz1                     1/1       Running   1          4d
kube-scheduler-centz2                     1/1       Running   1          4d
kubernetes-dashboard-8457c55f89-s5lwq     1/1       Running   0          4d
metrics-server-68d85f76bb-ql265           1/1       Running   0          13h <-- Metric Server
nginx-proxy-centz3                        1/1       Running   0          4d
nginx-proxy-centz4                        1/1       Running   0          4d
~~~~