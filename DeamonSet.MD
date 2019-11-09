### DaemonSets in K8
###### DaemonSets are K8 Controllers which aims to deploys 1 Pod/Node. The below guide aims to setup and understand DaemonSets in Kubernetes.

##### STEP#1: Create a DaemonSet:
```
vi daemonset.yml
```
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
```
kubectl apply -f daemonset.yml
```

##### STEP#2: Activate Node#2-Pod scheduling happens successfully:
![image](https://user-images.githubusercontent.com/45539698/68525669-e9274a80-02f9-11ea-9a51-b34ab0318cb7.png)
##### STEP#3: Activate Node#1-Pod scheduling happens successfully:
![image](https://user-images.githubusercontent.com/45539698/68525844-e3326900-02fb-11ea-9aaa-a08b46bf0035.png)

