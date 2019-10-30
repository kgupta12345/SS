```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulvaishnamespace
  
```
kubectl get namespaces
kubectl config set-context $(kubectl config current-context) --namespace=rahulvaishnamespace


## EXAMPLE#1
```


vi compresourcequota.yml


apiVersion: v1
kind: ResourceQuota
metadata:
  name: compresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    memory: "500Mi"
    limits.memory: "500Mi"
    requests.memory: "500Mi"
    cpu: "5"
    limits.cpu: "5"
    requests.cpu: "5"


kubectl apply -f compresourcequota.yml

```
```
bash-3.2$ kubectl -n rahulvaishnamespace describe quota compresourcequota
Name:            compresourcequota
Namespace:       rahulvaishnamespace
Resource         Used  Hard
--------         ----  ----
cpu              0     5
limits.cpu       0     5
limits.memory    0     500Mi
memory           0     500Mi
requests.cpu     0     5
requests.memory  0     500Mi
```

## EXAMPLE#2
```
vi podsresourcequota.yml

apiVersion: v1
kind: ResourceQuota
metadata:
  name: podsresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    pods: "2"

kubectl apply -f podsresourcequota.yml

```
```
bash-3.2$ kubectl -n rahulvaishnamespace describe quota podsresourcequota
Name:       podsresourcequota
Namespace:  rahulvaishnamespace
Resource    Used  Hard
--------    ----  ----
pods        0     2
bash-3.2$ 

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
  replicas: 1
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
 
 
 
