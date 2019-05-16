# CronJob

#### Basic cronjob
By default, cronjob will be create job every schedule running. So, you have duplicate pods. 
~~~~
NAME                            READY   STATUS      RESTARTS   AGE
pod/greeting-1557893580-mhj8v   0/1     Completed   0          92s
pod/greeting-1557893640-j5zpx   0/1     Completed   0          32s

NAME                            COMPLETIONS   DURATION   AGE
job.batch/greeting-1557893580   1/1           6s         92s <---
job.batch/greeting-1557893640   1/1           7s         32s <---

NAME                     SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/greeting   * * * * *   False     0        34s             2m
~~~~

#### Mandatory parameter cronjob

##### concurrencyPolicy
This parameter is responsible for running your job in parallel with other jobs.
- Allow : Allow job run parallels
- Forbid : Attempted to be scheduled when there was a previous schedule still running, then it would count as missed. 
- Replace : Replace previous job.

##### failedJobHistoryLimit
This paramenter is to keep failed job and deleting older entries.

##### successfulJobsHistoryLimit
This paramenter is to keep success job and deleting older entries.

##### suspend
If false, the cronjob is actively scheduling job, if true existing job will not be forced to terminate but new ones cannot be scheduled; this is useful for debugging when you want to stop your job.

### Optional parameter

#### activeDeadlineSeconds
Job doesn't kill or stop, but delete pods after error and scheduled in second.

#### startingDeadlineSeconds
If a job cannot be started in the interval between schedule time and startingDeadlineSeconds it will not start at all.

#### backoffLimit
Number of retries for pods launched by the job. If you want your pods to never restart (“restartPolicy : Never”), you need to set it at 0. 