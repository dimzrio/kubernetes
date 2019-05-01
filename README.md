# Namespace #

Get namespace
~~~~
$ kubectl get ns
~~~~

Create namespace applications
~~~~
$ kubectl create ns applications
~~~~

Set label name=apps to namespace
~~~~
$ kubectl label ns applications name=apps
~~~~

Get namespace by label
~~~~
$ kubectl get ns -l name=apps
~~~~

Delete namespace
~~~~
$ kubectl delete ns applications
~~~~


# Pods #

Get all pods 
~~~~
$ kubectl get pods
~~~~

Get all pods with details 
~~~~
$ kubectl get pods -o wide
~~~~

Get pods all namespace 
~~~~
$ kubectl get pods --all-namespaces
~~~~

Get pods with namespace applications
~~~~
$ kubectl get pods --namespace=applications
~~~~

Describe specific pod 
~~~~
$ kubectl describe pod <podname>
~~~~

Run command to specific pod 
~~~~
$ kubectl exec <podname> -it <command>
~~~~