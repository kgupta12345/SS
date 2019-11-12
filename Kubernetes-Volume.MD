### Kubernetes Volume
###### Volume refer to basic unit of storage. The below guide aims to setup and understand Volumes in Kubernetes.

##### Emptydir Volume: These Volumes are pod specific storage. 

| EXPERIMENT : To Understand Emptydir Volumes |
|---|

###### STEP#1: Create a Deployment:
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

![image](https://user-images.githubusercontent.com/45539698/68653982-f68b4180-0552-11ea-832c-3f8098746d7b.png)

![image](https://user-images.githubusercontent.com/45539698/68656889-ab742d00-0558-11ea-8aad-9e04e1bbff74.png)


![image](https://user-images.githubusercontent.com/45539698/68656775-7536ad80-0558-11ea-8349-e61d1336883b.png)



![image](https://user-images.githubusercontent.com/45539698/68658167-f0995e80-055a-11ea-811a-da960ab9584c.png)



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
![image](https://user-images.githubusercontent.com/45539698/68677301-e2ab0400-0581-11ea-94d5-ef10d6540737.png)
![image](https://user-images.githubusercontent.com/45539698/68677355-fce4e200-0581-11ea-93f2-acb4b960907e.png)


![image](https://user-images.githubusercontent.com/45539698/68681626-b98e7180-0589-11ea-9c0a-dd91ef7b5d5f.png)

![image](https://user-images.githubusercontent.com/45539698/68682079-8ef0e880-058a-11ea-8d65-3bd057f8f821.png)


![image](https://user-images.githubusercontent.com/45539698/68682079-8ef0e880-058a-11ea-8d65-3bd057f8f821.png)


![image](https://user-images.githubusercontent.com/45539698/68682775-abd9eb80-058b-11ea-9863-2a73172a6e92.png)


