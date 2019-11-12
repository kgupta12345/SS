### Kubernetes Job
###### Jobs in Kubernetes are like any other Batch & CronJobs.The below guide aims to setup and understand Jobs and CronJob in Kubernetes.

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
##### STEP#3: Create a ```Sequential``` Job:
```
vi kubejob.yml
```
```
apiVersion: batch/v1
kind: Job
metadata:
  name: kuberneteshelloworldjob
spec:
  completions: 2
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

| ANALYSIS: To Understand Job-Pod Relationship |
| --- |

###### *STEP#1: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                READY   STATUS              RESTARTS   AGE
pod/kuberneteshelloworldjob-z7p9v   0/1     ContainerCreating   0          4s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   49d

NAME                                COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob   0/2           4s         4s
</pre>
##### *```NOTE```: There is 1 Job (kuberneteshelloworldjob) which has to iterate twice. On its first iteration it instantiates Pod (kuberneteshelloworldjob-z7p9v)*
###### *STEP#2: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                READY   STATUS              RESTARTS   AGE
pod/kuberneteshelloworldjob-r9rk4   0/1     ContainerCreating   0          5s
pod/kuberneteshelloworldjob-z7p9v   0/1     Completed           0          38s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   49d

NAME                                COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob   1/2           38s        38s
</pre>
##### *```NOTE```: The first iteration of Job (kuberneteshelloworldjob) is completed and it's instantiated Pod(kuberneteshelloworldjob-z7p9v) is also completed. Now, the second iteration has started and it has instantiated its Pod (kuberneteshelloworldjob-r9rk4)*
###### *STEP#3: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-r9rk4   1/1     Running     0          33s
pod/kuberneteshelloworldjob-z7p9v   0/1     Completed   0          66s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   49d

NAME                                COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob   1/2           66s        66s
</pre>
##### *```NOTE```: The second iteration is running and also it's instantiated Pod (kuberneteshelloworldjob-r9rk4)*
###### *STEP#4: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-r9rk4   0/1     Completed   0          55s
pod/kuberneteshelloworldjob-z7p9v   0/1     Completed   0          88s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   49d

NAME                                COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob   2/2           67s        88s
bash-3.2$ 
</pre>
##### *```NOTE```: The second iteration is completed and also it's instantiated Pod (kuberneteshelloworldjob-r9rk4) is completed.*
###### *STEP#5: Get details of Job:*
<pre>
bash-3.2$ <b>kubectl describe job kuberneteshelloworldjob</b>
Name:           kuberneteshelloworldjob
Namespace:      default
Selector:       controller-uid=a4c0ad39-0205-11ea-8fa3-025000000001
Labels:         controller-uid=a4c0ad39-0205-11ea-8fa3-025000000001
                job-name=kuberneteshelloworldjob
Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                  {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"kuberneteshelloworldjob","namespace":"default"},"spec":{"backof...
Parallelism:    1
Completions:    2
Start Time:     Fri, 08 Nov 2019 14:26:25 +0530
Completed At:   Fri, 08 Nov 2019 14:27:32 +0530
Duration:       67s
Pods Statuses:  0 Running / 2 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=a4c0ad39-0205-11ea-8fa3-025000000001
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
  Type    Reason            Age    From            Message
  ----    ------            ----   ----            -------
  Normal  SuccessfulCreate  6m28s  job-controller  Created pod: kuberneteshelloworldjob-z7p9v
  Normal  SuccessfulCreate  5m55s  job-controller  Created pod: kuberneteshelloworldjob-r9rk4
bash-3.2$ 
</pre>

##### *```NOTE```: On describing Job (kuberneteshelloworldjob) it describes 2 Succeeded iterations and the pods which got created during each iteration -  kuberneteshelloworldjob-z7p9v and kuberneteshelloworldjob-r9rk4. Also, if we check logs of each pod, we see the application output:*
<pre>
bash-3.2$ <b>kubectl log kuberneteshelloworldjob-l6g7k</b>
log is DEPRECATED and will be removed in a future version. Use logs instead.
HELLO STEP#1
HELLO STEP#2
bash-3.2$ <b>kubectl log kuberneteshelloworldjob-tpb44</b>
log is DEPRECATED and will be removed in a future version. Use logs instead.
HELLO STEP#1
HELLO STEP#2
bash-3.2$ 
</pre>

##### *```NOTE```: After both Job iteartions are completed, the status becomes 2/2, which means, out of 2 Job iterations, both are completed. Also, as each iteration instantiated 1 Pod (each) , and their desired tasks are also completed, so 0/1 status comes against them.*

<br>

##### STEP#5: Create a ```Parallel``` Job: 
```
vi kubejobpar.yml
```
```
apiVersion: batch/v1
kind: Job
metadata:
  name: kuberneteshelloworldjob
spec:
  completions: 10
  parallelism: 2
  template:
    spec:
      containers:
      - name: kuberneteshelloworldjob
        image: rahulvaish/kuberneteshelloworldjob
      restartPolicy: Never
  backoffLimit: 4
```
```
kubectl apply -f kubejobpar.yml
```

| ANALYSIS: To Understand Job-Pod Relationship |
| --- |

###### *STEP#1: Get the status of Pods:*
<pre>
bash-3.2$ <b>kubectl get pods</b>
NAME                            READY   STATUS              RESTARTS   AGE
kuberneteshelloworldjob-24wxb   0/1     Completed           0          35s
kuberneteshelloworldjob-5d2lf   0/1     ContainerCreating   0          6s
kuberneteshelloworldjob-5q2st   0/1     Completed           0          2m20s
kuberneteshelloworldjob-ckv5p   0/1     ContainerCreating   0          0s
kuberneteshelloworldjob-fmlfc   0/1     Completed           0          74s
kuberneteshelloworldjob-kgjhp   0/1     Completed           0          69s
kuberneteshelloworldjob-n5cg2   0/1     Completed           0          2m20s
kuberneteshelloworldjob-ngw7v   0/1     Completed           0          107s
kuberneteshelloworldjob-sjnx7   0/1     Completed           0          40s
kuberneteshelloworldjob-xbjs8   0/1     Completed           0          102s
</pre>
###### *STEP#2: Get the status of Pods:*
<pre>
bash-3.2$ <b>kubectl get pods</b>
NAME                            READY   STATUS      RESTARTS   AGE
kuberneteshelloworldjob-24wxb   0/1     Completed   0          2m24s
kuberneteshelloworldjob-5d2lf   0/1     Completed   0          115s
kuberneteshelloworldjob-5q2st   0/1     Completed   0          4m9s
kuberneteshelloworldjob-ckv5p   0/1     Completed   0          109s
kuberneteshelloworldjob-fmlfc   0/1     Completed   0          3m3s
kuberneteshelloworldjob-kgjhp   0/1     Completed   0          2m58s
kuberneteshelloworldjob-n5cg2   0/1     Completed   0          4m9s
kuberneteshelloworldjob-ngw7v   0/1     Completed   0          3m36s
kuberneteshelloworldjob-sjnx7   0/1     Completed   0          2m29s
kuberneteshelloworldjob-xbjs8   0/1     Completed   0          3m31s
bash-3.2$ kubectl get jobs
NAME                      COMPLETIONS   DURATION   AGE
kuberneteshelloworldjob   10/10         2m54s      4m19s
bash-3.2$ 
</pre>

##### *```NOTE```: There is 1 Job, which expected to iterate 10 times. Each iteration will instantiate 1 Pod. Therefore, on introducing  parallelism of 2, initiates 2 iterations - 2 Pods at a time.*

<hr>

### CRON JOBS

##### STEP#1: Create a Job:
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

| ANALYSIS: To Understand Job-Pod Relationship |
| --- |

###### *STEP#1: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d
bash-3.2$ kubectl apply -f kubecronjob.yml 
cronjob.batch/kuberneteshelloworldjob created
</pre>

###### *STEP#2: Get the status of Job & Pods:*
<pre>
bash-3.2$  <b>kubectl get all</b>
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        <none>          5s
</pre>
##### *```NOTE```:CronJob gets scheduled for every 1 minute.*
###### *STEP#3: Get the status of Job & Pods:*
<pre>
bash-3.2$ <b>kubectl get all</b>
NAME                                           READY   STATUS              RESTARTS   AGE
pod/kuberneteshelloworldjob-1573148100-crn6j   0/1     ContainerCreating   0          6s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573148100   0/1           6s         6s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     1        11s             25s
</pre>
##### *```NOTE```:CronJob initiates a Job which instantiates a Pod.*
###### *STEP#4: Get details of Job:*
<pre>
bash-3.2$ <b>kubectl describe job kuberneteshelloworldjob-1573148100</b>
Name:           kuberneteshelloworldjob-1573148100
Namespace:      default
Selector:       controller-uid=ef8e3647-0184-11ea-8fa3-025000000001
Labels:         controller-uid=ef8e3647-0184-11ea-8fa3-025000000001
                job-name=kuberneteshelloworldjob-1573148100
Annotations:    <none>
Controlled By:  CronJob/kuberneteshelloworldjob
Parallelism:    1
Completions:    1
Start Time:     Thu, 07 Nov 2019 23:05:05 +0530
Completed At:   Thu, 07 Nov 2019 23:05:13 +0530
Duration:       8s
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=ef8e3647-0184-11ea-8fa3-025000000001
           job-name=kuberneteshelloworldjob-1573148100
  Containers:
   kuberneteshelloworldjob:
    Image:      rahulvaish/kuberneteshelloworldjob
    Port:       <none>
    Host Port:  <none>
    Args:
      /bin/sh
      -c
      echo The Meassage
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  22s   job-controller  <b>Created pod: kuberneteshelloworldjob-1573148100-crn6j</b>
bash-3.2$ 
</pre>
    
###### *STEP#5: Get the status of Pod:*
<pre>    
bash-3.2$ <b>kubectl describe pod kuberneteshelloworldjob-1573148100-crn6j</b>
Name:               kuberneteshelloworldjob-1573148100-crn6j
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               docker-desktop/192.168.65.3
Start Time:         Thu, 07 Nov 2019 23:05:05 +0530
Labels:             controller-uid=ef8e3647-0184-11ea-8fa3-025000000001
                    job-name=kuberneteshelloworldjob-1573148100
Annotations:        <none>
Status:             Succeeded
IP:                 10.1.5.183
Controlled By:      Job/kuberneteshelloworldjob-1573148100
Containers:
  kuberneteshelloworldjob:
    Container ID:  docker://307e11110dc6b48c50623f19dd262019ed801dfaec6c578c2ed81de9696aa796
    Image:         rahulvaish/kuberneteshelloworldjob
    Image ID:      docker-pullable://rahulvaish/kuberneteshelloworldjob@sha256:e732c3c2fcd16cd2b0baa7a738203003e0303c25aa4e89a07981fd3efa1c7079
    Port:          <none>
    Host Port:     <none>
    Args:
      /bin/sh
      -c
      echo The Meassage
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Thu, 07 Nov 2019 23:05:12 +0530
      Finished:     Thu, 07 Nov 2019 23:05:12 +0530
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8w2q2 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-8w2q2:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-8w2q2
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From                     Message
  ----    ------     ----  ----                     -------
  Normal  Scheduled  68s   default-scheduler        Successfully assigned default/kuberneteshelloworldjob-1573148100-crn6j to docker-desktop
  Normal  Pulling    67s   kubelet, docker-desktop  Pulling image "rahulvaish/kuberneteshelloworldjob"
  Normal  Pulled     61s   kubelet, docker-desktop  Successfully pulled image "rahulvaish/kuberneteshelloworldjob"
  Normal  Created    61s   kubelet, docker-desktop  Created container kuberneteshelloworldjob
  Normal  Started    61s   kubelet, docker-desktop  Started container kuberneteshelloworldjob  
</pre>

<hr>
