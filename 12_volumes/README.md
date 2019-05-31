## Volumes ##

### Host Path ###
A hostPath volume mounts a file or directory from the host node’s filesystem into your Pod.

Ref:
~~~~
https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
~~~~


### Persistent Volume ###
A cluster resource of how storage is provided from how it is consumed.

~~~~
$ kubectl apply -f persistentvolume/storage.yaml
persistentvolume/local-storage created
~~~~

~~~~
$ kubectl get pv -n applicaation                                                                                                                                                                               NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
local-storage   5Gi        RWO            Retain           Available           localstorage            88s
~~~~
