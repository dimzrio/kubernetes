# Readiness Probes #

The kubelet uses readiness probes to know when a Container is ready to start accepting traffic. A Pod is considered ready when all of its Containers are ready. When a Pod is not ready, it is removed from Service load balancers.

Actually, if you extending number of replicas the load balancer only check container are running, but not ensure the applications is ready to accept requests.

~~~~
$ kubectl apply -f nginx-readiness.yaml
deployment.apps/tomcat-deploy configured
service/tomcat-svc unchanged
~~~~

Test request with command in below. After that, you scale numbler of replicas in deployment and you can find downtime process.
~~~~
$ while true; do curl http://<ip-load-balancer> -I; sleep 0.1 ;echo; done

HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
Transfer-Encoding: chunked
Date: Sat, 08 Jun 2019 23:29:11 GMT

curl: (52) Empty reply from server <--- down time

HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
Transfer-Encoding: chunked
Date: Sat, 08 Jun 2019 23:29:14 GMT
~~~~

Uncomment readlinessProbe section.
~~~~
$ kubectl apply -f nginx-readiness.yaml
deployment.apps/tomcat-deploy configured
service/tomcat-svc unchanged
~~~~

# Liveness Probes #

The kubeless uses liveness probes to know when to restart container. Where an applications is running but unable to make requests.

In this example, i will create liveness probes for http index tomcat webserver.

~~~~
$ kubectl apply -f tomcat-liveness.yaml
deployment.apps/tomcat-deploy created
service/tomcat-svc created
~~~~

after container running and applications already accept request, delete directory ROOT index in container to make it failure.

~~~~
$ kubectl exec -it -n applications tomcat-deploy-598cb4b5b7-jnxdf -- rm -r /usr/local/tomcat/webapps/ROOT
~~~~

after around 10 seconds, container will be restarted.

~~~~
$ kubectl get pods -n applications
NAME                             READY   STATUS    RESTARTS   AGE
tomcat-deploy-598cb4b5b7-jnxdf   1/1     Running   1          8m47s <-- restarts by liveness probes
~~~~

~~~~
$ kubectl describe -n applications pod/tomcat-deploy-598cb4b5b7-jnxdf
....
Events:
  Type     Reason     Age                    From                          Message
  ----     ------     ----                   ----                          -------
  Normal   Scheduled  10m                    default-scheduler             Successfully assigned applications/tomcat-deploy-598cb4b5b7-jnxdf to pool-ntr9arwdn-ypcr
  Normal   Pulling    3m41s (x2 over 10m)    kubelet, pool-ntr9arwdn-ypcr  Pulling image "tomcat:latest"
  Warning  Unhealthy  3m41s (x3 over 4m1s)   kubelet, pool-ntr9arwdn-ypcr  Liveness probe failed: HTTP probe failed with statuscode: 404 <--- liveness probe
  Normal   Killing    3m41s                  kubelet, pool-ntr9arwdn-ypcr  Container tomcat failed liveness probe, will be restarted
  Normal   Pulled     3m37s (x2 over 9m59s)  kubelet, pool-ntr9arwdn-ypcr  Successfully pulled image "tomcat:latest"
  Normal   Created    3m37s (x2 over 9m58s)  kubelet, pool-ntr9arwdn-ypcr  Created container tomcat
  Normal   Started    3m37s (x2 over 9m58s)  kubelet, pool-ntr9arwdn-ypcr  Started container tomcat
....
~~~~

Ref: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/