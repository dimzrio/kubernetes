# ConfigMap #

A ConfigMap stores configuration settings that your Kubernetes Pods consume.

Get configmap
~~~~
$ kubectl get configmap -n applications
~~~~

Craete configmap from directory
~~~~
$ kubectl create configmap conf-properties --from-file=/data/ -n applications
~~~~

Create configmap from file
~~~~
$ kubectl create configmap conf-properties --from-file=/data/config.properties -n applications
~~~~

Create configmap from literals
~~~~
$ kubectl create configmap conf-properties --from-literal=APPS_ENV=productions --from-literal=APPS_VERSION=v1.0.0 -n applications
~~~~

Describe configmap
~~~~
$ kubectl describe configmap -n applications

Name:         nginx-env
Namespace:    applications
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"v1","data":{"APPS_ENV":"productions","APPS_VERSION":"v1.0.0"},"kind":"ConfigMap","metadata":{"annotations":{},"name":"nginx...

Data
====
APPS_ENV:
----
productions
APPS_VERSION:
----
v1.0.0
Events:  <none>
~~~~

Check env pods from configmap
~~~~
$ kubectl exec -n applications nginx-deploy-f868f566b-km6fs env

....
APPS_ENV=productions
APPS_VERSION=v1.0.0
....
~~~~