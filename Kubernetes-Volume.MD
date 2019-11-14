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

<hr>

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
kubectl apply -f hostpath.yml
```
###### *STEP#2: Check the Deployment status:*
![image](https://user-images.githubusercontent.com/45539698/68698705-84911780-05a7-11ea-899c-84f55d359bf8.png)
###### *STEP#3: Check hostPath (/volume/hostpath/) gets created on both Nodes(Node#1 and Node#2).* 
###### *STEP#4: Create node1.txt inside /volume/hostpath/ of Node#1 and node2.txt inside /volume/hostpath/ of Node#2:*
![image](https://user-images.githubusercontent.com/45539698/68682775-abd9eb80-058b-11ea-9863-2a73172a6e92.png)
###### *STEP#5: Navigate inside Pods:*
![image](https://user-images.githubusercontent.com/45539698/68682079-8ef0e880-058a-11ea-8d65-3bd057f8f821.png)
##### *```NOTE```: node1.txt, which we created under /volume/hostpath/ of Node#1 is visible from Pod's(vol-hp-b4b769c5c-9gttxf) directory structure /mnt-vol. This is because, in our deployment manifest, we have provided a mapping configuration between Pod's directory structure (/mnt-vol) with the  Node's directory structure (/volume/hostpath/). In this case, Pod(vol-hp-b4b769c5c-9gttxf) landed on Node#1 so Pod's directory path /mnt-vol got mapped with Node#1's directory path /volume/hostpath/. Hence, node1.txt present in /volume/hostpath/ of Node#1 becomes visible to Pod's(vol-hp-b4b769c5c-9gttxf) /mnt-vol.*

##### *Similarly, for node2.txt, which was created under /volume/hostpath/ of Node#2 is visible from Pod's(vol-hp-b4b769c5c-64fm7) directory structure /mnt-vol. The reason is same- in our deployment manifest, we have provided a mapping configuartion between Pod's directory structure (/mnt-vol) with the  Node's directory structure (/volume/hostpath/). In this case, Pod(vol-hp-b4b769c5c-64fm7)  landed on Node#2 so Pod's directory path /mnt-vol got mapped with Node#2's directory path /volume/hostpath/. Hence, node2.txt present in /volume/hostpath/ of Node#2 becomes visible to Pod's(vol-hp-b4b769c5c-64fm7) /mnt-vol.*

<hr>

| EXPERIMENT : To Understand Persistent Volumes & Persistent Volume Claims |
|---|
##### Persistent Volumes are used the same way as other volumes that we described earlier. But the pod needn’t care about how the storage shall be provisioned to it. The cluster administrator creates the volumes and pods can access them through Persistent Volume Claims, a level of abstraction between the volume and its storage mechanism.
###### *STEP#1:  Create a PV:*
```
vi pvhp.yml
```
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pvhp
spec:
  storageClassName: pvhp
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/pvhp/data"
```
```
kubectl apply -f pvhp.yml
```
###### *STEP#2:  Create a PVC:*
```
vi pvc.yml
```
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvchp
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: pvhp
```
```
kubectl apply -f pvc.yml
```
#### * ```NOTE```: The PVC is searching for a PV with storageClassName “pvhp” and it is requesting a 1 GB storage from this volume.*
![image](https://user-images.githubusercontent.com/45539698/68702706-47308800-05af-11ea-8cc1-221932ff5cee.png)
###### *STEP#3: Create a Deployment :*
```
vi pvdep.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pvdep
  labels:
    app: pvdep
spec:
  replicas: 2
  selector:
    matchLabels:
      app: pvdep
  template:
    metadata:
      labels:
        app: pvdep
    spec:
      containers:
      - name: pvdep
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: pvdep
          mountPath: "/mnt-pvdep/"
      volumes:
      - name: pvdep
        persistentVolumeClaim:
            claimName: pvchp
```	
```
kubectl apply -f pvdep.yml
```
###### *STEP#4: Insider Pod pvdep-7fd966f564-m8t9l, create a file:*
![image](https://user-images.githubusercontent.com/45539698/68705968-05a2db80-05b5-11ea-903e-7030f96c60fc.png)
##### *```NOTE```: The PV we chose is of type HostPath. In our case, there are two Pods, one Pod gets created Node#1, while anther Pod gets created on Node#2. We created a file inside Node#2's Pod on location /mnt-pvdep/. The created file can be accessed  by any other Pod which gets landed on Node#2.*
![image](https://user-images.githubusercontent.com/45539698/68706481-f2444000-05b5-11ea-8e21-c15ace7b2013.png)
##### *```NOTE```: Here, the new Pod which lands on Node#2 can access the file, as it too mounts on the same location /mnt-pvdep/. Also, to note that PV maintains a level of abstraction, which means /pvhp/data can be located.*



<hr>


