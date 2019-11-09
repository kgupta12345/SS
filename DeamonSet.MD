
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: springbootdockerdaemonset
spec:
  selector:
    matchLabels:
      demotype: springbootdockerdaemonset
  template:
    metadata:
      labels:
        demotype: springbootdockerdaemonset
    spec:
      containers:
      - image: rahulvaish/springbootdocker
        name: springbootdockerdaemonset
```
