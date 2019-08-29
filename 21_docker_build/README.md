# Docker Build 

For build docker image in kubernetes we have 2 alretnative way.

### Docker Engine
~~~~
$ kubectl apply -f build-image.yaml
pod/build-image created
~~~~

### Kaniko
Create secret for private docker registry
~~~~
$ kubectl create secret docker-registry docker-credentials --docker-username=test --docker-password=testing123  --docker-server=example.com:5000
secret/docker-credentials created
~~~~

~~~~
$ kubectl get secret docker-credentials -o yaml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJkcmVnaXN0cnkuZGV0aWs.....
kind: Secret
metadata:
  creationTimestamp: "2019-08-29T04:42:26Z"
  name: docker-credentials
  namespace: default
  resourceVersion: "5344385"
  selfLink: /api/v1/namespaces/default/secrets/docker-credentials
  uid: 66785a5b-ca17-11e9-a056-080027ee73ba
type: kubernetes.io/dockerconfigjson
~~~~

~~~~
$ kubectl apply -f build-kaniko.yaml
pod/build-image created
~~~~

You can check log process with tail command.
~~~~
$ kubectl logs --tail 100 -f build-image
INFO[0000] Resolved base name alpine:latest to alpine:latest 
INFO[0000] Resolved base name alpine:latest to alpine:latest 
INFO[0000] Downloading base image alpine:latest         
INFO[0004] Error while retrieving image from cache: getting file info: stat /cache/sha256:acd3ca9941a85e8ed16515bfc5328e4e2f8c128caa72959a58a127b7801ee01f: no such file or directory 
INFO[0004] Downloading base image alpine:latest         
INFO[0007] Built cross stage deps: map[]
.....
~~~~