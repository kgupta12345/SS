```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulvaishnamespace
  
 ```
```
kubectl get namespaces

vi memquota.yml


apiVersion: v1
kind: ResourceQuota
metadata:
  name: memresourcequota
  namespace: rahulvaishnamespace
spec:
  hard:
    limits.memory: "500Mi"



kubectl apply -f memquota.yml


kubectl -n rahulvaishnamespace describe quota memresourcequota



```
