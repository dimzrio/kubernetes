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
$ kubectl get pods -n applications
~~~~

Describe specific pod 
~~~~
$ kubectl describe pod <podname>
~~~~

Run command to specific pod 
~~~~
$ kubectl exec <podname> -it <command>
~~~~