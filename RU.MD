
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
      app: d
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
