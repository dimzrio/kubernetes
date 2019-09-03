# Volumes #

### Host Path ###
A hostPath volume mounts a file or directory from the host node’s filesystem into your Pod.

Ref:
~~~~
https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
~~~~

### EmptyDir ###
For example, i have pods with 2 different type (Local disk & Memory).
~~~~
$ kubectl apply -f emptydir/.
pod/nginx-pod-01 created
pod/nginx-pod-02 created
~~~~

Local Disk
~~~~
$ kubectl exec -it nginx-pod-01 -- df -h
Filesystem                Size      Used Available Use% Mounted on
....
/dev/mapper/centos-root
                         50.0G     13.9G     36.1G  28% /data
....
~~~~

Memory 
~~~~
$ kubectl exec -it nginx-pod-02 -- df -h
Filesystem                Size      Used Available Use% Mounted on
....
tmpfs                     1.8G         0      1.8G   0% /data
....
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

# Volume Plugins #
Kubernetes have many volume plugins.

### Remote Storage 
- GCE Persistent Disk (GCE-PD)
- AWS Elastic Block Storage
- Azure File Storage
- iSCSI
- NFS
- Ceph
- GlusterFS

### Ephemeral Storage
- EmptyDir
- Expose Kubernetes API (Secret & ConfigMap)

### Local Persistent Volume

### Out-of-Tree
- Flex (Exec a binary)
- CSI (Container Storage Interface)

### Other
- Host Path