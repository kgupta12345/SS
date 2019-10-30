```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulvaishnamespace
  
 ```
```
kubectl get namespaces

vi compresourcequota.yml


apiVersion: v1
kind: ResourceQuota
metadata:
  name: computeresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    limits.memory: "500Mi"
    requests.memory: "200Mi"
    limits.cpu: "5"
    requests.cpu: "2"



kubectl apply -f compresourcequota.yml


kubectl -n rahulvaishnamespace describe quota computeresourcequota



```
```
bash-3.2$ kubectl -n rahulvaishnamespace describe quota computeresourcequota
Name:            computeresourcequota
Namespace:       rahulvaishnamespace
Resource         Used  Hard
--------         ----  ----
limits.cpu       0     5
limits.memory    0     500Mi
requests.cpu     0     2
requests.memory  0     200Mi
bash-3.2$ 
```


