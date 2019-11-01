
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
        app: d
    spec:
      containers:
      - name: e
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080




```
