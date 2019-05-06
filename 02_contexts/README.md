# Contexts #

View config
~~~~
$ kubectl config view

....
cluster: cluster.local
user: kubernetes-admin
....
~~~~

Get all contexts
~~~~
$ kubectl config get-contexts
~~~~

Get current contesx
~~~~
$ kubectl config current-context
~~~~

Create context for namespaces applications and kube-system
~~~~
$ kubectl config set-context applications --namespace=applications --user=kubernetes-admin --cluster=cluster.local
$ kubectl config set-context kube-system --namespace=kube-system --user=kubernetes-admin --cluster=cluster.local
~~~~

Use context
~~~~
$ kubectl config use-context applications
~~~~