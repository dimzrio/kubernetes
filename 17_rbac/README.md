# Role Base Access Control (RBAC) #

Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise.

RBAC in kubernetes have 4 top-level type:
- Role
- RoleBinding
- ClusterRole
- ClusterRoleBinding

# Role # 
a role contains rules that represent a set of permissions of specific namespaces.

~~~~
$ kubectl apply -f applications-role.yaml
role.rbac.authorization.k8s.io/readonly-role created
~~~~

~~~~
$ kubectl describe role -n applications readonly-role
Name:         readonly-role
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"Role","metadata":{"annotations":{},"name":"readonly-role","namespace":"applications"}...
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 []              [get watch list]
~~~~

https://jeremievallee.com/2018/05/28/kubernetes-rbac-namespace-user.html

# Cluster Role #
a cluster role same like role, but they are cluster scope. Like get nodes, namespaces resources. etc.