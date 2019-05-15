# CronJob

Basic cronjob
~~~~
NAME                            READY   STATUS      RESTARTS   AGE
pod/greeting-1557893580-mhj8v   0/1     Completed   0          92s
pod/greeting-1557893640-j5zpx   0/1     Completed   0          32s

NAME                            COMPLETIONS   DURATION   AGE
job.batch/greeting-1557893580   1/1           6s         92s
job.batch/greeting-1557893640   1/1           7s         32s

NAME                     SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/greeting   * * * * *   False     0        34s             2m   <-- cronjob every minutes
~~~~