To accessing kubernetes api have 2 way for authentications.
- Service Account
- User Account

# Service Account #
Service account is an authentication handled by kubernetes for give a permission to accessing a pod.
Processes in containers inside pods can also contact the apiserver. When they do, they are authenticated as a particular Service Account. 

~~~~
$ kubectl apply -f service_account/applications-sa.yaml
serviceaccount/dimzrio-sa created
role.rbac.authorization.k8s.io/readonly-role created
rolebinding.rbac.authorization.k8s.io/readonly-binding created
~~~~

In above example, will create service account dimzrio-sa with permissions readonly for namespace applications.

~~~~
$ kubectl get sa -n applications
NAME         SECRETS   AGE
default      1         21d
dimzrio-sa   1         50m
~~~~

#### Set Service Account as Kubectl Config.

Get tokens.
~~~~
$ kubectl describe sa dimzrio-sa -n applications
Name:                dimzrio-sa
Namespace:           applications
Labels:              <none>
Annotations:         kubectl.kubernetes.io/last-applied-configuration:
                       {"apiVersion":"v1","kind":"ServiceAccount","metadata":{"annotations":{},"name":"dimzrio-sa","namespace":"applications"}}
Image pull secrets:  <none>
Mountable secrets:   dimzrio-sa-token-dxhwp 
Tokens:              dimzrio-sa-token-dxhwp <----
Events:              <none>
~~~~

Get User Token.
~~~~
$ kubectl get secret dimzrio-sa-token-dxhwp -n applications -o "jsonpath={.data.token}" | base64 -d
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9......
~~~~

Get Certificate Token.
~~~~
$ kubectl get secret dimzrio-sa-token-dxhwp -n applications -o "jsonpath={.data['ca\.crt']}"
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0t......
~~~~

Create Kube Config.
~~~~
apiVersion: v1
kind: Config
preferences: {}

# Define the cluster
clusters:
- cluster:
    certificate-authority-data: PLACE CERTIFICATE HERE
    server: https://YOUR_KUBERNETES_API_ENDPOINT
  name: dimzrio-cluster-do

# Define the user
users:
- name: dimzrio-sa
  user:
    as-user-extra: {}
    client-key-data: PLACE CERTIFICATE HERE
    token: PLACE USER TOKEN HERE

# Define the context: linking a user to a cluster
contexts:
- context:
    cluster: dimzrio-cluster-do
    namespace: applications
    user: dimzrio-sa
  name: applications

# Define current context
current-context: applications
~~~~

# User Account #
User account is an authentication handled by normal user in server. When you (a human) access the cluster (for example, using kubectl), you are authenticated by the apiserver as a particular User Account (usually admin). If you create normal user in host, you MUST setup ssl certificate to connect to api server.

Ref:
https://jeremievallee.com/2018/05/28/kubernetes-rbac-namespace-user.html