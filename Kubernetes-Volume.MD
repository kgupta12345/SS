### Kubernetes Volume
###### Volumes means storage. The below guide aims to setup and understand various kinds of Volumes in Kubernetes. 

| EXPERIMENT : To Understand Emptydir Volumes |
|---|
##### Emptydir are temporary storage, which resides on RAM and are specific to Pods. They are non-persistent strorage units.
###### *STEP#1:  Create a Deployment:*
```
vi emptydir.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vol-ed
  labels:
    app: vol-ed
spec:
  replicas: 2
  selector:
    matchLabels:
      app: vol-ed
  template:
    metadata:
      labels:
        app: vol-ed
    spec:
      containers:
      - name: vol-ed
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: vol-ed
          mountPath: "/volumepath/emptydirvol"
      volumes:
      - name: vol-ed
        emptyDir: {}       
```
```
kubectl apply -f emptydir.yml
```
###### *STEP#2: Analyse inside Pods &rightarrow; The file created inside first Pod Volume is not visible to another Pod:*
![image](https://user-images.githubusercontent.com/45539698/68653982-f68b4180-0552-11ea-832c-3f8098746d7b.png)
###### *STEP#3: Delete the Pod &rightarrow; The file created inside the Pod volume, also gets deleted:*
![image](https://user-images.githubusercontent.com/45539698/68656889-ab742d00-0558-11ea-8aad-9e04e1bbff74.png)
###### *STEP#4: Shutdown Node2 &rightarrow; New Pods re-creates on Node1 making it as STEP#3:*
![image](https://user-images.githubusercontent.com/45539698/68658167-f0995e80-055a-11ea-811a-da960ab9584c.png)

| EXPERIMENT : To Understand HostPath Volumes |
|---|
##### HostPaths are storage that gets created on HostMachine/Nodes. HostPath Volumes are persistent in nature.
###### *STEP#1:  Create a Deployment:*
```
vi hostpath.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vol-hp
  labels:
    app: vol-hp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: vol-hp
  template:
    metadata:
      labels:
        app: vol-hp
    spec:
      containers:
      - name: vol-hp
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: vol-hp
          mountPath: "/mnt-vol"
      volumes:
      - name: vol-hp
        hostPath:
            path: "/volume/hostpath/"

```
```
kubectl apply f hostpath.yml
```
###### *STEP#2: Check the Deployment status:*
![image](https://user-images.githubusercontent.com/45539698/68698705-84911780-05a7-11ea-899c-84f55d359bf8.png)
###### *STEP#3: Check the hostPath on both Nodes &rightarrow Path /volume/hostpath/ gets created.* 
###### *STEP#4: Create files inside hostPath Volumes:*
![image](https://user-images.githubusercontent.com/45539698/68682775-abd9eb80-058b-11ea-9863-2a73172a6e92.png)
###### *STEP#5: Observe the files created in STEP#4, appears inside Pods at path /mnt-vol:*
![image](https://user-images.githubusercontent.com/45539698/68682079-8ef0e880-058a-11ea-8d65-3bd057f8f821.png)

| EXPERIMENT : To Understand Persistent Volumes |
|---|
