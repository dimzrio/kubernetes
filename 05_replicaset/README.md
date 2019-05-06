# ReplicaSet #

~~~~
$ kubectl get all -n applications -o wide 

NAME                     READY     STATUS    RESTARTS   AGE       IP             NODE
pod/webserver-rs-lppxs   1/1       Running   0          2s        10.233.85.46   centz3

NAME                           DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES         SELECTOR
replicaset.apps/webserver-rs   1         1         1         2s        nginx        nginx:alpine   app in (nginx-services,webserver)
~~~~