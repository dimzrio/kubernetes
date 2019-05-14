# JOB #

Basic jobs
~~~~
$ kubectl logs greeting-62vnw -n applications
Hello Kubernetes!!!
~~~~

Completions Jobs is a simple case to ensure job successfully teriminate with specified number.
~~~~
$ kubectl get pods -n applications

NAME             READY   STATUS      RESTARTS   AGE
greeting-9bbll   0/1     Completed   0          16s  <-- this pod run after pods greeting-t58j8
greeting-t58j8   0/1     Completed   0          25s
~~~~

Parallelism Job is an completions jobs running as parralels
~~~~
$ kubectl get pods -n applications

NAME                 READY   STATUS      RESTARTS   AGE
pod/greeting-npv69   0/1     Completed   0          37s
pod/greeting-swnp6   0/1     Completed   0          49s
pod/greeting-z6zll   0/1     Completed   0          49s
pod/greeting-ttdmk   0/1     Completed   0          43s <--- Run parallels
pod/greeting-9qpbc   0/1     Completed   0          43s <--- Run parallels

NAME                 COMPLETIONS   DURATION   AGE
job.batch/greeting   5/5           18s        50s
~~~~

Backofflimit Jobs is as limit to create pods if have an error
~~~~
$ kubectl get pods -n applications

NAME                 READY   STATUS   RESTARTS   AGE
pod/greeting-n7kvj   0/1     Error    0          47s
pod/greeting-w7q72   0/1     Error    0          41s
pod/greeting-x9hls   0/1     Error    0          31s

NAME                 COMPLETIONS   DURATION   AGE
job.batch/greeting   0/1           47s        47s
~~~~

ActiveDeadLineSeconds Jobs is similar with timeout of pods
~~~~
$ kubectl get pods -n applications

NAME                 READY   STATUS        RESTARTS   AGE
pod/greeting-lz2km   1/1     Terminating   0          11s <--- deadline with 10s

NAME                 COMPLETIONS   DURATION   AGE
job.batch/greeting   0/1           26s        26s
~~~~