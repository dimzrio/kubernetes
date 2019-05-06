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

Get all
~~~~
$ kubectl get all -n applications -o wide

NAME                                    READY     STATUS    RESTARTS   AGE       IP              NODE
pod/webserver-deploy-78f9dd7454-mgzgq   1/1       Running   0          7m        10.233.102.50   centz4

NAME                               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES         SELECTOR
deployment.apps/webserver-deploy   1         1         1            1           7m        nginx        nginx:alpine   app=nginx-deploy

NAME                                          DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES         SELECTOR
replicaset.apps/webserver-deploy-78f9dd7454   1         1         1         7m        nginx        nginx:alpine   app=nginx-deploy,pod-template-hash=78f9dd7454
~~~~