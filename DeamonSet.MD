### DaemonSets in K8
###### DaemonSets are K8 Controllers which aims to deploys 1 Pod/Node. The below guide aims to setup and understand DaemonSets in Kubernetes.

| EXPERIMENT : To Understand DaemonSets |
|---|
###### *STEP#1: Create a DaemonSet:*
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

###### *STEP#2: Activate Node#2 &rightarrow; Pod scheduling happens successfully:*
![image](https://user-images.githubusercontent.com/45539698/68526137-b5025880-02fe-11ea-8c61-0a38053b222e.png)
###### *STEP#3: Activate Node#1 &rightarrow; Pod scheduling happens successfully:*
![image](https://user-images.githubusercontent.com/45539698/68526045-b0897000-02fd-11ea-98dc-1e5a23244f87.png)
###### *STEP#4: Kill a Pod &rightarrow; Re-creation of new Pod happens on the same Node:*
![image](https://user-images.githubusercontent.com/45539698/68526480-5f2faf80-0302-11ea-91c4-8fef86ccf0a1.png)
###### *STEP#5: Running DeamonSet on selective Node:*
 - ###### *STEP#A: Label the Node with *gpupresent=true*:*
    ![image](https://user-images.githubusercontent.com/45539698/68527410-930fd280-030c-11ea-9273-b7c044ba88fb.png)
 - ###### *STEP#B: Re-deploy DaemonSet by introducing *nodeselector* in the configuration:*
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
          nodeSelector:
            gpupresent: "true"
      ```
      ```
      kubectl apply -f daemonset.yml
      ```
 - ###### *STEP#C: Observe Pod Scheduling happening only on Node#2:*
    ![image](https://user-images.githubusercontent.com/45539698/68527597-a1f78480-030e-11ea-853e-04ff1e988f70.png)


