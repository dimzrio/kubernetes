# Secret #


User: admin, Pass: dimzrio123

~~~~
$ echo -n "admin" | base64
YWRtaW4=

$ echo -n "dimzrio123" | base64
ZGltenJpbzEyMw==
~~~~

Get secret
~~~~
$ kubectl get secret -n applications
~~~~

Create secret
~~~~
$ kubectl create secret generic mongo-secret --from-file=./credentials -n applications
~~~~
