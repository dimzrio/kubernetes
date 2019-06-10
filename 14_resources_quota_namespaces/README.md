# Resources Quota & Limits #

When you create namespace, you can optionally specify how much CPU and memory (RAM) for your namespace with resources quota.
You can set limit resouces for a pon in namespace resouces.

Create resouces quotas.
~~~~
$ kubectl apply -f resource-quota-ns.yaml
resourcequota/applications-quota created
~~~~

Create resouces limits.
~~~~
$ kubectl apply -f limit-range-pod.yaml 
limitrange/applications-limit created
~~~~

Describe namespaces.
~~~~
$ kubectl describe ns applications
Name:         applications
Labels:       <none>
Annotations:  <none>
Status:       Active

Resource Quotas
 Name:            applications-quota
 Resource         Used  Hard
 --------         ---   ---
 limits.cpu       0     2
 limits.memory    0     2Gi
 requests.cpu     0     1
 requests.memory  0     1Gi

Resource Limits
 Type       Resource  Min   Max  Default Request  Default Limit  Max Limit/Request Ratio
 ----       --------  ---   ---  ---------------  -------------  -----------------------
 Container  cpu       100m  1    300m             500m           -
 Container  memory    10Mi  1Gi  32Mi             512Mi          -
~~~~

## Example ##

Create pods with default request/limits.
~~~~
$ kubectl apply -f examples/default-limits.yaml
pod/nginx created
~~~~

Describes pods.
~~~~
$ kubectl describe pod/nginx -n applications
....
  Limits:
    cpu:     500m
    memory:  512Mi
  Requests:
    cpu:        300m
    memory:     32Mi
  Environment:  <none>
....
~~~~

Create pods (Error) with less then minimum cpu requests. 
~~~~
$ kubectl apply -f examples/less-minimum-cpu.yaml
Error from server (Forbidden): error when creating "14_resources_quota_namespaces/examples/less-minimum-cpu.yaml": pods "nginx" is forbidden: minimum cpu usage per Container is 100m, but request is 50m
~~~~

Create pods (Error) with greather then maximum limit memory requests.
~~~~
$ kubectl apply -f examples/over-maximum-memory.yaml
The Pod "nginx" is invalid: spec.containers[0].resources.requests: Invalid value: "600Mi": must be less than or equal to memory limit
~~~~




