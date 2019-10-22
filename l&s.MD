```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: a
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

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: f
  name: g
  namespace: default
spec:
  type: NodePort
  ports:
  - nodePort: 31743
    port: 8080
    protocol: TCP
    name: http
    targetPort: 8080
  selector:
    app: e

```

- kubectl get deployment -> a
- kubectl get pods -> a-5cbdc789c7-sjwx4
- kubectl get rs -> a-5cbdc789c7
- kubectl get services ->  g

