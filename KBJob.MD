### Kubernetes Job


##### STEP#1: Create a Java Class:
```
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
```
##### STEP#2: Create a Dockerfile:
```
FROM java:8  
COPY . /var/www/java  
WORKDIR /var/www/java  
RUN javac KubernetesHelloWorldJob.java  
CMD ["java", "KubernetesHelloWorldJob"]
```
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
bash-3.2$ kubectl get pods
NAME                            READY   STATUS      RESTARTS   AGE
kuberneteshelloworldjob-8dvvw   0/1     Completed   0          12m
bash-3.2$ kubectl logs kuberneteshelloworldjob-8dvvw
HELLO STEP#1
HELLO STEP#2
bash-3.2$ 

</pre>
