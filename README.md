# Namespace #

Get namespace
~~~~
$ kubectl get ns
~~~~

Create namespace applications
~~~~
$ kubectl create ns applications
~~~~

Set label name=apps to namespace applications
~~~~
$ kubectl label ns applications name=apps
~~~~

Get namespace by label
~~~~
$ kubectl get ns -l name=apps
~~~~

Delete namespace applications
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


# Deployment #

Get deployment
~~~~
$ kubectl get deploy -n applications
~~~~

Scale replicas deployment
~~~~
$ kubectl scale --replicas=4 deploy nginx-deploy -n applications
~~~~

Show revision history rollout
~~~~
$ kubectl rollout history deploy nginx-deploy -n applications
~~~~

Undo rollout to revision history
~~~~
$ kubectl rollout undo deploy nginx-deploy --namespace=applications --to-revision=1
~~~~

