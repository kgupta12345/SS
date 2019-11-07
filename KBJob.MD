### Kubernetes Job

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
