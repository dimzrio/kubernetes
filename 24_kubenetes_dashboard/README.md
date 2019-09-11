# Kubernetes Dashboard

Kubernetes Dashboard is a WEB UI for manage your cluster.

### Deploy Kubernetes
Get dashboard version from https://github.com/kubernetes/dashboard/releases.
~~~~
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard unchanged
serviceaccount/kubernetes-dashboard unchanged
service/kubernetes-dashboard unchanged
secret/kubernetes-dashboard-certs unchanged
secret/kubernetes-dashboard-csrf configured
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
secret/kubernetes-dashboard-key-holder configured
configmap/kubernetes-dashboard-settings unchanged
role.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
deployment.apps/kubernetes-dashboard unchanged
service/dashboard-metrics-scraper unchanged
deployment.apps/dashboard-metrics-scraper unchanged
~~~~

Note: You must install metric-server to enable graphic

Edit service to NodePort
~~~~
$ kubectl edit svc -n kubernetes-dashboard kubernetes-dashboard

  ports:
  - nodePort: 30006 <---- add nodeport
    port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: NodePort <---- Change From ClusterIP to NodePort
~~~~

How dashboard can be accessing by url: https://IP_ADDR:30006/

### Get token full access
~~~~
$ kubectl -n kube-system describe secret clusterrole-aggregation-controller | grep token:
token:      eyJhbGciOiJSUzI1NiIsImtpZC......
~~~~

### Create service account for readonly dashboard
~~~~
$ kubectl apply -f readonly/readonly.yaml 
namespace/development created
serviceaccount/readonly created
clusterrole.rbac.authorization.k8s.io/readonly-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/readonly-dashboard created
~~~~

### Create service account full access only in namespace development
~~~~
$ kubectl apply -f grantaccess/developer-sa.yaml
namespace/development created
serviceaccount/developer created
role.rbac.authorization.k8s.io/developer created
rolebinding.rbac.authorization.k8s.io/developer created
clusterrole.rbac.authorization.k8s.io/developer-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/developer-dashboard created
~~~~

### Get token
~~~~
$ kubectl get secret -n development
NAME                    TYPE                                  DATA   AGE
default-token-9b9mt     kubernetes.io/service-account-token   3      8s
developer-token-ksq5b   kubernetes.io/service-account-token   3      8s

$ kubectl get secret developer-token-ksq5b -n development -o "jsonpath={.data.token}" | base64 -d
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9......
~~~~