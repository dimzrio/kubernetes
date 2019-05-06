Get all
~~~~
$ kubectl get all -o wide --show-labels

NAME                 READY   STATUS    RESTARTS   AGE   IP              NODE     NOMINATED NODE   READINESS GATES
pod/nginx-ds-jrjgk   1/1     Running   0          24s   10.233.85.50    centz3   <none>           <none>
pod/nginx-ds-wtqgq   1/1     Running   0          24s   10.233.102.55   centz4   <none>           <none>

NAME                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE   CONTAINERS   IMAGES         SELECTOR
daemonset.apps/nginx-ds   2         2         2       2            2           <none>          24s   nginx        nginx:alpine   app=nginx
~~~~
