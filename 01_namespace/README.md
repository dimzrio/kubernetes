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