# Volumes #

### Host Path ###
A hostPath volume mounts a file or directory from the host node’s filesystem into your Pod.

Ref:
~~~~
https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
~~~~

### Persistent Volume ###
A cluster resource of how storage is provided from how it is consumed.

~~~~
$ kubectl apply -f 12_volumes/persistentvolume/pv-hostpath.yaml
persistentvolume/local-storage created
~~~~

~~~~
$ kubectl get pv -n applications
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
local-storage   5Gi        RWO            Delete           Available           localstorage            49s
~~~~

Persistent volume have 3 method for reclaim policy pvc object when a user done with their volume.

##### Retain
The "Retain" reclaim policy allows for manual reclamation of the resource. When the PersistentVolumeClaim is deleted, the PersistentVolume still exists and the volume is considered "released". So, an administrator should be manualy claim volume. (Default)

An administrator can manually reclaim the volume with the following steps:
- Delete persistent volume manualy
- Manually clean up the data on the associated storage asset accordingly. (If you using hostpath, you should delete manualy contain of hostpath)
- Recreate persistent volume if you want to reuse.

##### Delete
The "Delete" reclaim policy allows for automatically remove PersistentVolume after delete PersistentVolumeClaim.
For this options, pv with hostpath only support in /tmp/. if you not set in /tmp/ you can getting error pv cannot deleted.

PersistentVolume Event Error:
~~~~
Events:
  Type     Reason              Age   From                         Message
  ----     ------              ----  ----                         -------
  Warning  VolumeFailedDelete  17s   persistentvolume-controller  host_path deleter only supports /tmp/.+ but received provided /mnt/pvdevices
~~~~

##### Recycle
You can reuse persistentvolume with clean data. It means, recycle policy perform a basic stub (rm -rf /volume/*). 

A PersistentVolume can be mounted on a host in any way supported by the resource provider.

The access modes are:
- ReadWriteOnce(RWO): the volume can be mounted as read-write by a single node
- ReadOnlyMany(ROX): the volume can be mounted read-only by many nodes
- ReadWriteMany(RWX): the volume can be mounted as read-write by many nodes