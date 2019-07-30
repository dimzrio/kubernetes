# Gitlab + Kubernetes #

Crete .gitlab-ci.yaml
~~~~
stages:
  - test
  
iresizer-deploy-testing:
  stage: test
  image:
    name: lachlanevenson/k8s-kubectl:latest
    entrypoint: ["/bin/sh", "-c"]
  stage: test
  script:
    - kubectl version
  tags:
    - testing-runner
~~~~

Create namespaces
~~~~
$ kubectl apply -f namespace.yaml
namespace/applications created
~~~~

Create service account
~~~~
$ kubectl apply -f serviceaccount.yaml
serviceaccount/applications-sa created
~~~~

Create rbac for service account
~~~~
$ kubectl apply -f rbac.yaml
clusterrolebinding.rbac.authorization.k8s.io/applications-clusterrolebinding created
rolebinding.rbac.authorization.k8s.io/applications-rolebinding created
~~~~

## Add existing kubernetes cluster ##
1. Navigate to your project’s Operations > Kubernetes page.
2. Click Add Kubernetes cluster.
3. Click Add an existing Kubernetes cluster and fill in the details:
   - Kubernetes cluster name (required) - The name you wish to give the cluster.
   - Environment scope (required) - The associated environment to this cluster.
   ~~~~
   https://docs.gitlab.com/ee/user/project/clusters/#setting-the-environment-scope-premium
   ~~~~

   - API URL - It’s the URL that GitLab uses to access the Kubernetes API. 
   ~~~~
   $ kubectl cluster-info | grep 'Kubernetes master' | awk '/http/ {print $NF}'
   ~~~~

   - CA certificate (required) - A valid Kubernetes certificate is needed to authenticate to the K8S cluster.
   Copy certificate.
   ~~~~
   $ kubectl get secrets
   NAME                  TYPE                                  DATA   AGE
   default-token-mlxpn   kubernetes.io/service-account-token   3      6d23h

   $ kubectl get secret default-token-mlxpn -o jsonpath="{['data']['ca\.crt']}" | base64 --decode
   -----BEGIN CERTIFICATE-----
   MIICyDCCAbCgAw.....
   -----END CERTIFICATE-----
   ~~~~

   - Token - GitLab authenticates against Kubernetes using service tokens, which are scoped to a particular namespace.
   Copy from token.
   ~~~~
   $ kubectl -n applications describe secret $(kubectl -n applications get secret | grep applications-sa | awk '{print $1}')
   Name:         applications-sa-token-xqchs
   Namespace:    applications
   Labels:       <none>
   Annotations:  kubernetes.io/service-account.name: applications-sa
                 kubernetes.io/service-account.uid: ed20eadd-b280-11e9-9b98-080027ee73ba   
   Type:         kubernetes.io/service-account-token   
   Data
   ====
   ca.crt:     1025 bytes
   namespace:  12 bytes
   token:      eyJhbGciOiJSUzI1Ni.....
   ~~~~

   - Project namespace (optional) - You don’t have to fill it in; by leaving it blank, GitLab will create one for you. Also each project should have a unique namespace.

## Setup Runner ##
Copy cartificate kubernetes to server runner.
~~~~
$ mkdir /home/gitlab-runner/kubernetes/
$ scp root@IPMASTER:/etc/kubernetes/pki/ca.crt /home/gitlab-runner/kubernetes/
~~~~

Register Runner
~~~~
gitlab-runner register \
  --non-interactive \
  --url "GITLAB-URL" \
  --registration-token "GITLAB-TOKEN" \
  --executor "kubernetes" \
  --description "kubernetes-runner" \
  --tag-list "testing-runner" \
  --kubernetes-host "K8S-HOST" \
  --kubernetes-namespace "applications" \
  --kubernetes-ca-file "/home/gitlab-runner/kubernetes/ca.crt" \
  --kubernetes-bearer_token "SERVICEACCOUNT-TOKEN"
~~~~