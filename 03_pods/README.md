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

Exec to specific container in pods
~~~~
$ kubectl apply -f multiple-container/.
pod/webserver created
~~~~

webserver pod have 2 container (nginx & tomcat)
~~~~
NAME        READY   STATUS    RESTARTS   AGE
webserver   2/2     Running   0          76s
~~~~

exec to nginx
~~~~
$ kubectl exec -it webserver -c nginx -- ls
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
~~~~

exec to tomcat
~~~~
$ kubectl exec -it webserver -c tomcat -- ls -l
total 124
-rw-r--r--. 1 root root  19534 Aug 14 22:27 BUILDING.txt
-rw-r--r--. 1 root root   5407 Aug 14 22:27 CONTRIBUTING.md
-rw-r--r--. 1 root root  57011 Aug 14 22:27 LICENSE
-rw-r--r--. 1 root root   1726 Aug 14 22:27 NOTICE
-rw-r--r--. 1 root root   3255 Aug 14 22:27 README.md
-rw-r--r--. 1 root root   7139 Aug 14 22:27 RELEASE-NOTES
-rw-r--r--. 1 root root  16262 Aug 14 22:27 RUNNING.txt
drwxr-xr-x. 2 root root   4096 Aug 22 00:26 bin
drwxr-sr-x. 1 root root     22 Sep  3 04:26 conf
drwxr-sr-x. 2 root staff    78 Aug 22 00:26 include
drwxr-xr-x. 2 root root   4096 Aug 22 00:26 lib
drwxrwxrwx. 1 root root    177 Sep  3 04:26 logs
drwxr-sr-x. 3 root staff   151 Aug 22 00:26 native-jni-lib
drwxr-xr-x. 2 root root     30 Aug 22 00:26 temp
drwxr-xr-x. 7 root root     81 Aug 14 22:24 webapps
drwxrwxrwx. 1 root root     22 Sep  3 04:26 work
~~~~