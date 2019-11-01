#### ResourceQuota is a practice to draw boundaries on Namespaces (in a cluster). To understand, let's see an example:

##### STEP#1: Create a Namespace:
```
vi rahulvaishnamespace.yml
```
```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulvaishnamespace
  
```
```
kubectl apply -f rahulvaishnamespace.yml
```
##### STEP#2: Switch on the newly created Namespace:
```
kubectl config set-context $(kubectl config current-context) --namespace=rahulvaishnamespace
```

##### STEP#3: Create a Pod Quota:
```
vi podsresourcequota.yml
```
```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: podsresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    pods: "2"
```
```
kubectl apply -f podsresourcequota.yml
```
###### Checking:
```
____________________________________________________________________________________________________
bash-3.2$ kubectl -n rahulvaishnamespace describe quota podsresourcequota
Name:       podsresourcequota
Namespace:  rahulvaishnamespace
Resource    Used  Hard
--------    ----  ----
pods        0     2
bash-3.2$ 
____________________________________________________________________________________________________
```
##### STEP#4: Create a Deployment
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: a
  namespace: rahulvaishnamespace
  labels:
    app: b
spec:
  replicas: 3
  selector:
    matchLabels:
      app: d
  template:
    metadata:
      labels:
        app: d
    spec:
      containers:
      - name: e
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
```
###### Checking: Replicas=3 | RQ caps on 2.  
```
_______________________________________________________________________________________________________________________________________________________________________
bash-3.2$ kubectl -n rahulvaishnamespace describe quota podsresourcequota
Name:       podsresourcequota
Namespace:  rahulvaishnamespace
Resource    Used  Hard
--------    ----  ----
pods        0     2
bash-3.2$ kubectl apply -f d.yml
deployment.extensions/a created
bash-3.2$ kubectl -n rahulvaishnamespace describe quota podsresourcequota
Name:       podsresourcequota
Namespace:  rahulvaishnamespace
Resource    Used  Hard
--------    ----  ----
pods        2     2
bash-3.2$ kubectl -n rahulvaishnamespace describe deployment a
Name:                   a
Namespace:              rahulvaishnamespace
CreationTimestamp:      Wed, 30 Oct 2019 16:54:24 +0530
Labels:                 app=b
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"extensions/v1beta1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"b"},"name":"a","namespace":"rahulvais...
Selector:               app=d
Replicas:               3 desired | 2 updated | 2 total | 2 available | 1 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:  app=d
  Containers:
   e:
    Image:        rahulvaish/springbootdocker
________________________________________________________________________________________________________________________________________________________________________
```
###### Checking: What happend to the 3rd pod ?
```
_______________________________________________________________________________________________________________________________________________________________________________________________________________
bash-3.2$ kubectl get rs
NAME           DESIRED   CURRENT   READY   AGE
a-5cbdc789c7   3         2         2       32m
bash-3.2$ kubectl -n rahulvaishnamespace describe rs a-5cbdc789c7 | less
Name:           a-5cbdc789c7
Namespace:      rahulvaishnamespace
Selector:       app=d,pod-template-hash=5cbdc789c7
Labels:         app=d
                pod-template-hash=5cbdc789c7
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/a
Replicas:       2 current / 3 desired
Pods Status:    2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=d
           pod-template-hash=5cbdc789c7
  Containers:
   e:
    Image:        rahulvaish/springbootdocker
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type             Status  Reason
  ----             ------  ------
  ReplicaFailure   True    FailedCreate
Events:
  Type     Reason            Age                From                   Message
  ----     ------            ----               ----                   -------
  Warning  FailedCreate      38m                replicaset-controller  Error creating: pods "a-5cbdc789c7-2zc59" is forbidden: exceeded quota: podsresourcequota, requested: pods=1, used: pods=2, limited: pods=2
  Warning  FailedCreate      38m                replicaset-controller  Error creating: pods "a-5cbdc789c7-dtl72" is forbidden: exceeded quota: podsresou:
_______________________________________________________________________________________________________________________________________________________________________________________________________________
```
*WHATIF: Create a Deployment (Replicas=3) before setting the Resource Quota (Pod=2):*
```
____________________________________________________________________________________________________
bash-3.2$ kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
a      3/3     3            3           3m57s
bash-3.2$ kubectl -n rahulvaishnamespace describe quota podsresourcequota
Name:       podsresourcequota
Namespace:  rahulvaishnamespace
Resource    Used  Hard
--------    ----  ----
pods        3     2
____________________________________________________________________________________________________
```

##### STEP#5: Create a Compute Quota: 
##### [THE BELOW EXPERIENT HAS BEEN CARRIED ON MY UBUNTU MACHINE]
```
vi compresourcequota.yml
```
```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    limits.memory: "2Gi"
    limits.cpu: "2000m"
    requests.cpu: "1000m"
    requests.memory: "1Gi"
```
```
kubectl apply -f compresourcequota.yml
```

##### STEP#6: Make a Deployment:
```
vi deployment.yml
```
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: a
  namespace: rahulvaishnamespace
  labels:
    app: b
spec:
  replicas: 2
  selector:
    matchLabels:
      app: d
  template:
    metadata:
      labels:
        app: d
    spec:
      containers:
      - name: e
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "500Mi"
            cpu: "200m"
          limits:
            memory: "1Gi"
            cpu: "400m"
```
```
kubectl apply -f deployment.yml
```
###### Checking:
```
____________________________________________________________________________________________________
[root@master K8Experiments]# kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
a      2/2     2            2           17s


[root@master K8Experiments]# kubectl get pods
NAME                 READY   STATUS    RESTARTS   AGE
a-55d4bbb78d-gxk6d   1/1     Running   0          66s
a-55d4bbb78d-nqb4f   1/1     Running   0          66s


[root@master K8Experiments]# kubectl -n rahulvaishnamespace describe quota compresourcequota
Name:            compresourcequota
Namespace:       rahulvaishnamespace
Resource         Used    Hard
--------         ----    ----
limits.cpu       800m    2
limits.memory    2Gi     2Gi
requests.cpu     400m    1
requests.memory  1000Mi  1Gi
[root@master K8Experiments]# 
____________________________________________________________________________________________________

========================
O B S E R V A T I O N S:
========================


2 Replicas means 2 Pods.
POD1's MAX CPU =400m
POD2's MAX CPU =400m 
400m + 400m = 800m. 
This demand (800m) gets booked from Namespace's 2000m.
Result:
Resource         Used    Hard
--------         ----    ----
limits.cpu       800m    2 (2000m)


2 Replicas means 2 Pods.
POD1's MAX MEM =1Gi
POD2's MAX MEM =1Gi 
1Gi + 1Gi = 2Gi
This demand (2Gi) gets booked from Namespace's 2Gi.
Result:
Resource         Used    Hard
--------         ----    ----
limits.memory    2Gi     2Gi


2 Replicas means 2 Pods.
POD1's REQUIRES CPU =200m
POD2's REQUIRES CPU =200m
200m + 200m = 400m
This demand (400m) gets booked from Namespace's 1000m.
Result:
Resource         Used    Hard
--------         ----    ----
requests.cpu     400m    1 (1000m)



2 Replicas means 2 Pods.
POD1's REQUIRES MEM =500Mi
POD2's REQUIRES MEM =500Mi
500Mi + 500Mi = 1000Mi
This demand (1000Mi) gets booked from Namespace's 1Gi.
Result:
Resource         Used    Hard
--------         ----    ----
requests.memory  1000Mi (1Gi)  1Gi


```
###### On increasing replicas=5:

```
____________________________________________________________________________________________________

[root@master K8Experiments]# kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
a      2/5     2            2           34s


[root@master K8Experiments]# kubectl get pods
NAME                 READY   STATUS    RESTARTS   AGE
a-55d4bbb78d-fr7kp   1/1     Running   0          36s
a-55d4bbb78d-j8tns   1/1     Running   0          36s


[root@master K8Experiments]# kubectl get rs
NAME           DESIRED   CURRENT   READY   AGE
a-55d4bbb78d   5         2         2       42s


[root@master K8Experiments]# kubectl describe rs a-55d4bbb78d
Name:           a-55d4bbb78d
Namespace:      rahulvaishnamespace
Selector:       app=d,pod-template-hash=55d4bbb78d
Labels:         app=d
                pod-template-hash=55d4bbb78d
Annotations:    deployment.kubernetes.io/desired-replicas: 5
                deployment.kubernetes.io/max-replicas: 7
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/a
Replicas:       2 current / 5 desired
Pods Status:    2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=d
           pod-template-hash=55d4bbb78d
  Containers:
   e:
    Image:      rahulvaish/springbootdocker
    Port:       8080/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     400m
      memory:  1Gi
    Requests:
      cpu:        200m
      memory:     500Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type             Status  Reason
  ----             ------  ------
  ReplicaFailure   True    FailedCreate
Events:
  Type     Reason            Age                From                   Message
  ----     ------            ----               ----                   -------
  Normal   SuccessfulCreate  61s                replicaset-controller  Created pod: a-55d4bbb78d-fr7kp
  Warning  FailedCreate      61s                replicaset-controller  Error creating: pods "a-55d4bbb78d-mj6xm" is forbidden: exceeded quota: compresourcequota, requested: limits.memory=1Gi,requests.memory=500Mi, used: limits.memory=2Gi,requests.memory=1000Mi, limited: limits.memory=2Gi,requests.memory=1Gi
  Normal   SuccessfulCreate  61s                replicaset-controller  Created pod: a-55d4bbb78d-j8tns
  Warning  FailedCreate      61s                replicaset-controller  Error creating: pods "a-55d4bbb78d-8rx8r" is forbidden: exceeded quota: compresourcequota, requested: limits.memory=1Gi,requests.memory=500Mi, used: limits.memory=2Gi,requests.memory=1000Mi, limited: limits.memory=2Gi,requests.memory=1Gi
  Warning  FailedCreate      61s                replicaset-controller  Error creating: pods "a-55d4bbb78d-r24wv" is forbidden: exceeded quota: compresourcequota, requested: limits.memory=1Gi,requests.memory=500Mi, used: limits.memory=2Gi,requests.memory=1000Mi, limited: limits.memory=2Gi,requests.memory=1Gi

____________________________________________________________________________________________________
```
