
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

 "strategy": {
      "type": "RollingUpdate",
      "rollingUpdate": {
        "maxUnavailable": "25%",
        "maxSurge": "25%"
      }
    },
    "revisionHistoryLimit": 10,
    "progressDeadlineSeconds": 600
    
    
    

strategy.rollingUpdate.maxSurge: 0
strategy.rollingUpdate:maxUnavailable: 1
minReadySeconds: 5
revisionHistoryLimit: 10 
