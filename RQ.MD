##### ResourceQuota is a practice to draw boundaries in a cluster. It is applied on a Namespaces. To understand, let's see an example:

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
    limits.cpu: "2"
    requests.cpu: "1"
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
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1"


```
```
kubectl apply -f deployment.yml
```

