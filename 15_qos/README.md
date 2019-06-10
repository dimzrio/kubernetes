# QoS (Quality of Service) #

When Kubernetes creates a Pod it assigns one of these QoS classes to the Pod:

- Guaranteed
- Burstable
- BestEffort

### Guaranteed ###
- Every Container in the Pod must have a memory limit and a memory request, and they must be the same.
- Every Container in the Pod must have a CPU limit and a CPU request, and they must be the same.

~~~~
$ kubectl apply -f guaranteed.yaml
pod/nginx-guaranteed created
~~~~

~~~~
$ kubectl describe pods -n applications nginx-guaranteed
....
Volumes:
  default-token-847xv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-847xv
    Optional:    false
QoS Class:       Guaranteed
Node-Selectors:  <none>
....
~~~~

### Brustable ###
- The Pod does not meet the criteria for QoS class Guaranteed.
- At least one Container in the Pod has a memory or CPU request.

~~~~
$ kubectl apply -f brustable.yaml
pod/nginx-brustable created
~~~~

~~~~
$ kubectl describe pods -n applications nginx-brustable
....
Volumes:
  default-token-847xv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-847xv
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
....
~~~~

### Besteffort ###
- The Pod must not have any memory or CPU limits or requests.

~~~~
$ kubectl apply -f besteffort.yaml
pod/nginx-besteffort created
~~~~

~~~~
$ kubectl describe pods -n applications nginx-besteffort
....
Volumes:
  default-token-847xv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-847xv
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
....
~~~~