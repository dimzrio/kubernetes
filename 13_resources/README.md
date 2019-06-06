## Resources ##

When you specify a Pod, you can optionally specify how much CPU and memory (RAM) each Container needs.

Chack resource nodes.

~~~~
$ kubectl describe nodes pool-ntr9arwdn-ypcb
.....
Capacity:
 attachable-volumes-csi-dobs.csi.digitalocean.com:  7
 cpu:                                               2 <--- 2 Cores CPU
 ephemeral-storage:                                 61893400Ki <--- 60Gb
 hugepages-1Gi:                                     0
 hugepages-2Mi:                                     0
 memory:                                            2043188Ki <--- 2Gb Ram

Non-terminated Pods:         (6 in total)
  Namespace                  Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                ------------  ----------  ---------------  -------------  ---
  kube-system                cilium-fzgwj                        300m (15%)    0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                cilium-operator-5469488bbb-mwmv9    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                coredns-5f44b47f5f-bnn5r            100m (5%)     0 (0%)      70Mi (3%)        170Mi (8%)     11d
  kube-system                csi-do-node-zrxdd                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                do-node-agent-scbcs                 102m (5%)     102m (5%)   80Mi (4%)        100Mi (5%)     11d
  kube-system                kube-proxy-rtg2x                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d

Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource                                          Requests    Limits
  --------                                          --------    ------
  cpu                                               502m (25%)  102m (5%) <-- Already use 502m (0.5) for kube-system with limit 102m (0.1) of cores.
  memory                                            150Mi (7%)  270Mi (14%) <-- Already use 150Mb for kube-system, kube-system can't use memory over 270Mb.
  ephemeral-storage                                 0 (0%)      0 (0%)
  attachable-volumes-csi-dobs.csi.digitalocean.com  0           0
Events:                                             <none>
 .....
~~~~

### Requests ###

~~~~
$ kubectl apply -f nginx-requests.yaml
deployment.apps/nginx created
~~~~

in deployment above will be create 2 container which have allocate 512 Mb of memory and 0.5 of cores for each.
~~~~
$ kubectl describe nodes pool-ntr9arwdn-ypcb
.....
Non-terminated Pods:         (8 in total)
  Namespace                  Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                ------------  ----------  ---------------  -------------  ---
  applications               nginx-58c68ffcff-rdzj9              500m (25%)    1 (50%)     512Mi (27%)      1Gi (54%)      33s 
  applications               nginx-58c68ffcff-s2tsf              500m (25%)    1 (50%)     512Mi (27%)      1Gi (54%)      33s
  kube-system                cilium-fzgwj                        300m (15%)    0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                cilium-operator-5469488bbb-mwmv9    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                coredns-5f44b47f5f-bnn5r            100m (5%)     0 (0%)      70Mi (3%)        170Mi (8%)     11d
  kube-system                csi-do-node-zrxdd                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                do-node-agent-scbcs                 102m (5%)     102m (5%)   80Mi (4%)        100Mi (5%)     11d
  kube-system                kube-proxy-rtg2x                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
.....
~~~~

if you modified number of replicas over of resouses in nodes. you can't create containter to exceeds resouces of nodes.
for example i will create 3 replicas of nginx. it's will exceeds of request resources of nodes.

~~~~
$ kubectl get all -n applications
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-58c68ffcff-2vfvn   0/1     Pending   0          3m14s <-- container pending
pod/nginx-58c68ffcff-rdzj9   1/1     Running   0          12m
pod/nginx-58c68ffcff-s2tsf   1/1     Running   0          12m

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   2/3     3            2           12m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-58c68ffcff   3         3         2       12m
~~~~

~~~~
$ kubectl describe -n applications pod/nginx-58c68ffcff-2vfvn
Events:
  Type     Reason            Age                  From               Message
  ----     ------            ----                 ----               -------
  Warning  FailedScheduling  89s (x4 over 4m10s)  default-scheduler  0/2 nodes are available: 1 Insufficient cpu, 1 node(s) didn't match node selector.
~~~~

~~~~
$ kubectl describe nodes pool-ntr9arwdn-ypcb
Non-terminated Pods:         (8 in total)
  Namespace                  Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                ------------  ----------  ---------------  -------------  ---
  applications               nginx-7c886f6947-cprbl              500m (25%)    0 (0%)      512Mi (27%)      0 (0%)         10s
  applications               nginx-7c886f6947-hbdcv              500m (25%)    0 (0%)      512Mi (27%)      0 (0%)         10s
  kube-system                cilium-fzgwj                        300m (15%)    0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                cilium-operator-5469488bbb-mwmv9    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                coredns-5f44b47f5f-bnn5r            100m (5%)     0 (0%)      70Mi (3%)        170Mi (8%)     11d
  kube-system                csi-do-node-zrxdd                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d
  kube-system                do-node-agent-scbcs                 102m (5%)     102m (5%)   80Mi (4%)        100Mi (5%)     11d
  kube-system                kube-proxy-rtg2x                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         11d

Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource                                          Requests      Limits
  --------                                          --------      ------
  cpu                                               1502m (75%)   102m (5%) <--- 1 container pending, because only 498m (0.49) of 2000m (2Gb) core is available. But, container need 0.5 of core.
  memory                                            1174Mi (61%)  270Mi (14%)
  ephemeral-storage                                 0 (0%)        0 (0%)
  attachable-volumes-csi-dobs.csi.digitalocean.com  0             0
~~~~

### Limits ###

- If memory exceeds of limit the container will be killed (restarted) - OOMKilled (Out Of Memory).
- If cpu exceeds of limit the container still running but is clamped.

~~~~
$ kubectl apply -f nginx-requests.yaml
deployment.apps/nginx created
~~~~

~~~~
$ kubectl get all -n applications
NAME                         READY   STATUS      RESTARTS   AGE
pod/nginx-f8fb8b7f6-b9ctb    0/1     Pending     0          8m40s
pod/nginx-f8fb8b7f6-khclj    1/1     Running     0          8m40s
pod/nginx-f8fb8b7f6-rzrcw    1/1     Running     0          8m40s
pod/tomcat-b5dc65bf4-2z4v7   0/1     OOMKilled   1          56s
~~~~