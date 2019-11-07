### Kubernetes Job

###### BATCH JOBS

##### STEP#1: Create a Java Class:
<pre>
public class KubernetesHelloWorldJob {
    public static void main(String[] args) {
        System.out.println("HELLO STEP#1");
        try {
            Thread.sleep(25000);
        } catch (Exception exception) {
            System.out.println("ExceptionCaught");
        }
        System.out.println("HELLO STEP#2");

    }
}
</pre>
##### STEP#2: Create a Dockerfile:
<pre>
FROM java:8  
COPY . /var/www/java  
WORKDIR /var/www/java  
RUN javac KubernetesHelloWorldJob.java  
CMD ["java", "KubernetesHelloWorldJob"]
</pre>
##### STEP#3: Create a Job:
```
vi kubejob.yml
```
```
apiVersion: batch/v1
kind: Job
metadata:
  name: kuberneteshelloworldjob
spec:
  template:
    spec:
      containers:
      - name: kuberneteshelloworldjob
        image: rahulvaish/kuberneteshelloworldjob
      restartPolicy: Never
  backoffLimit: 4
```
```
kubectl apply -f kubejob.yml
```

##### STEP#4: Check & Analyse:
<pre>
bash-3.2$ <b>kubectl get job</b>
NAME                      COMPLETIONS   DURATION   AGE
kuberneteshelloworldjob   1/1           31s        34s
loadtest                  1/1           45s        38d
bash-3.2$ <b>kubectl describe job kuberneteshelloworldjob</b>
Name:           kuberneteshelloworldjob
Namespace:      default
Selector:       controller-uid=f2032f31-0121-11ea-b943-025000000001
Labels:         controller-uid=f2032f31-0121-11ea-b943-025000000001
                job-name=kuberneteshelloworldjob
Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                  {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"kuberneteshelloworldjob","namespace":"default"},"spec":{"backof...
Parallelism:    1
Completions:    1
Start Time:     Thu, 07 Nov 2019 <b>11:16:29</b> +0530
Completed At:   Thu, 07 Nov 2019 <b>11:17:00</b> +0530
Duration:       31s
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=f2032f31-0121-11ea-b943-025000000001
           job-name=kuberneteshelloworldjob
  Containers:
   kuberneteshelloworldjob:
    Image:        rahulvaish/kuberneteshelloworldjob
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  37s   job-controller  Created pod: kuberneteshelloworldjob-8dvvw
bash-3.2$ 
</pre>

###### Also:
<pre>
bash-3.2$ <b>kubectl get pods</b>
NAME                            READY   STATUS      RESTARTS   AGE
kuberneteshelloworldjob-8dvvw   0/1     Completed   0          12m
bash-3.2$ <b>kubectl logs kuberneteshelloworldjob-8dvvw</b>
HELLO STEP#1
HELLO STEP#2
bash-3.2$ 

</pre>
##### NOTE: After the job is completed the Job status becomes 1/1 but the pod status becomes 0/1.

<hr>

###### CRON JOBS

##### STEP#3: Create a Job:
```
vi kubecronjob.yml
```
```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: kuberneteshelloworldjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: kuberneteshelloworldjob
            image: rahulvaish/kuberneteshelloworldjob
            args:
            - /bin/sh
            - -c
            - echo The Message
          restartPolicy: OnFailure
```
```
kubectl apply -f kubecronjob.yml
```
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                           READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-1573146840-4lnx8   0/1     Completed   0          2m30s
pod/kuberneteshelloworldjob-1573146900-68wr6   0/1     Completed   0          89s
pod/kuberneteshelloworldjob-1573146960-dbw5d   0/1     Completed   0          29s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573146840   1/1           9s         2m31s
job.batch/kuberneteshelloworldjob-1573146900   1/1           8s         90s
job.batch/kuberneteshelloworldjob-1573146960   1/1           8s         30s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        35s             3m27s


bash-3.2$ <b>kubectl get all</b>
NAME                                           READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-1573146900-68wr6   0/1     Completed   0          2m53s
pod/kuberneteshelloworldjob-1573146960-dbw5d   0/1     Completed   0          113s
pod/kuberneteshelloworldjob-1573147020-5l2br   0/1     Completed   0          53s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573146900   1/1           8s         2m53s
job.batch/kuberneteshelloworldjob-1573146960   1/1           8s         113s
job.batch/kuberneteshelloworldjob-1573147020   1/1           9s         53s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        58s             4m50s


bash-3.2$ <b>kubectl get all</b>
NAME                                           READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-1573147080-4dv4h   0/1     Completed   0          2m14s
pod/kuberneteshelloworldjob-1573147140-r6hdj   0/1     Completed   0          74s
pod/kuberneteshelloworldjob-1573147200-hv6g9   0/1     Completed   0          14s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573147080   1/1           8s         2m14s
job.batch/kuberneteshelloworldjob-1573147140   1/1           8s         74s
job.batch/kuberneteshelloworldjob-1573147200   1/1           9s         14s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        19s             7m11s
bash-3.2$ 


</pre>
