# StatefulSet

StatefulSet is a Kubernetes resource used to manage stateful applications. Statefulset doesn't create replicaset like deployment, it will create replica pod with increment number end of name pods.

This example i will configure hostpath dynamically PersistentVolume.
I'm recommended you to install hostpath provisiner.

~~~~
https://github.com/rimusz/charts/tree/master/stable/hostpath-provisioner
~~~~

Afterward, you have storage class with name hostpath by default.
~~~~
$ kubectl get sc -o yaml
apiVersion: v1
items:
- apiVersion: storage.k8s.io/v1
  kind: StorageClass
  metadata:
    annotations:
      storageclass.kubernetes.io/is-default-class: "true"
    creationTimestamp: "2019-09-02T03:29:44Z"
    labels:
      app.kubernetes.io/instance: hostpath-provisioner
      app.kubernetes.io/managed-by: Tiller
      app.kubernetes.io/name: hostpath-provisioner
      helm.sh/chart: hostpath-provisioner-0.2.3
    name: hostpath
    resourceVersion: "5962031"
    selfLink: /apis/storage.k8s.io/v1/storageclasses/hostpath
    uid: e847180b-cd31-11e9-a056-080027ee73ba
  provisioner: hostpath
  reclaimPolicy: Delete
  volumeBindingMode: Immediate
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
~~~~

Create statefulset
~~~~
$ kubectl apply -f nginx-sts.yaml
service/nginx-headless created
statefulset.apps/nginx-sts created
~~~~

In command above, each container of statefulset automatic provision persistance volume.
~~~~
$ kubectl get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE
persistentvolume/pvc-2ce394fb-cd57-11e9-a056-080027ee73ba   100Mi      RWO            Delete           Bound    default/www-nginx-sts-0   hostpath                21s
persistentvolume/pvc-32fe8126-cd57-11e9-a056-080027ee73ba   100Mi      RWO            Delete           Bound    default/www-nginx-sts-1   hostpath                3s

NAME                                    STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/www-nginx-sts-0   Bound    pvc-2ce394fb-cd57-11e9-a056-080027ee73ba   100Mi      RWO            hostpath       21s
persistentvolumeclaim/www-nginx-sts-1   Bound    pvc-32fe8126-cd57-11e9-a056-080027ee73ba   100Mi      RWO            hostpath       11s
~~~~