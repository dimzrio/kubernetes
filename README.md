Kubernetes cheat sheet

### Namespace ###

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
