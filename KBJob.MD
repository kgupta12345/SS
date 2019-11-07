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
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d
bash-3.2$ kubectl apply -f kubecronjob.yml 
cronjob.batch/kuberneteshelloworldjob created



bash-3.2$  <b>kubectl get all</b>
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        <none>          5s
<b>[ABOVE: THE JOB GETS CREATED]</b>



bash-3.2$ <b>kubectl get all</b>
NAME                                           READY   STATUS              RESTARTS   AGE
pod/kuberneteshelloworldjob-1573148100-crn6j   0/1     ContainerCreating   0          6s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573148100   0/1           6s         6s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     1        11s             25s
<b>[ABOVE: THE JOB INITIATES POD]</b>



bash-3.2$ kubectl describe job kuberneteshelloworldjob-1573148100
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
  Normal  SuccessfulCreate  22s   job-controller  Created pod: kuberneteshelloworldjob-1573148100-crn6j
bash-3.2$ 
bash-3.2$ 
bash-3.2$ 
bash-3.2$ kubectl get all
NAME                                           READY   STATUS      RESTARTS   AGE
pod/kuberneteshelloworldjob-1573148100-crn6j   0/1     Completed   0          28s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   48d

NAME                                           COMPLETIONS   DURATION   AGE
job.batch/kuberneteshelloworldjob-1573148100   1/1           8s         28s

NAME                                    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/kuberneteshelloworldjob   */1 * * * *   False     0        33s             47s
bash-3.2$ kubectl describe pod kuberneteshelloworldjob-1573148100-crn6j
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
  Normal  Scheduled  46s   default-scheduler        Successfully assigned default/kuberneteshelloworldjob-1573148100-crn6j to docker-desktop
  Normal  Pulling    45s   kubelet, docker-desktop  Pulling image "rahulvaish/kuberneteshelloworldjob"
  Normal  Pulled     39s   kubelet, docker-desktop  Successfully pulled image "rahulvaish/kuberneteshelloworldjob"
  Normal  Created    39s   kubelet, docker-desktop  Created container kuberneteshelloworldjob
  Normal  Started    39s   kubelet, docker-desktop  Started container kuberneteshelloworldjob
bash-3.2$ kubectl describe pod kuberneteshelloworldjob-1573148100-crn6j
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
  Normal  Scheduled  52s   default-scheduler        Successfully assigned default/kuberneteshelloworldjob-1573148100-crn6j to docker-desktop
  Normal  Pulling    51s   kubelet, docker-desktop  Pulling image "rahulvaish/kuberneteshelloworldjob"
  Normal  Pulled     45s   kubelet, docker-desktop  Successfully pulled image "rahulvaish/kuberneteshelloworldjob"
  Normal  Created    45s   kubelet, docker-desktop  Created container kuberneteshelloworldjob
  Normal  Started    45s   kubelet, docker-desktop  Started container kuberneteshelloworldjob
bash-3.2$ kubectl describe pod kuberneteshelloworldjob-1573148100-crn6j
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
bash-3.2$ kubectl describe logs  kuberneteshelloworldjob-1573148100-crn6j
error: the server doesn't have a resource type "logs"
bash-3.2$ kubectl logs  kuberneteshelloworldjob-1573148100-crn6j
The Meassage
bash-3.2$ kubectl logs  pod kuberneteshelloworldjob-1573148100-crn6j
Error from server (NotFound): pods "pod" not found
bash-3.2$ 
</pre>
