
```


apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: appdeploymentfromyml
  labels:
    app: appdeploymentfromyml
spec:
  replicas: 4
  selector:
    matchLabels:
      app: appdeploymentfromyml
  selector:
    matchLabels:
      app: appdeploymentfromyml
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 0
      maxUnavailable: 1
  minReadySeconds: 5
  revisionHistoryLimit: 10    
  template:
    metadata:
      labels:
        app: appdeploymentfromyml
    spec:
      containers:
      - name: appdeploymentfromyml
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080

```
```

apiVersion: v1
kind: Service
metadata:
  labels:
    app: appservicefromyml
  name: appservicefromyml
  namespace: default
spec:
  type: NodePort
  ports:
  - nodePort: 31746
    port: 8080
    protocol: TCP
    name: http
    targetPort: 8080
  selector:
    app: appdeploymentfromyml

```

strategy.type: RollingUpdate - There is another strategy- Recreate which will come with the cost of downtime. It will shoutdown all replicas and then updated and recreate.

strategy.type: RollingUpdate - 0 Downtime. Example: Let say we have 100 Machines. RU will update first 10%, then another 10%.......

    

During the update process - When the existing pods are getting terminated and new ones are getting created, the state of application remains as the previous one. Once all existing pods are terminated and new once are born , the stae of the application is updated (to new one).

We can check the rollout activity by:
```
kubectl rollout status deployment appdeploymentfromyml
```

Each time we do rollout activity, a new replicaSet is created. Below are the commands, which states the changes in the Rollout activity:
```
kubectl get rs
kubectl rollout history deployment appdeploymentfromyml
kubectl rollout history deployment appdeploymentfromyml --revision 2
```

