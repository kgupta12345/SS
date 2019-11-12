### Kubernetes Volume
######

##### Emptydir Volume:

##### USECASE#1: 
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
![image](https://user-images.githubusercontent.com/45539698/68653982-f68b4180-0552-11ea-832c-3f8098746d7b.png)

![image](https://user-images.githubusercontent.com/45539698/68656889-ab742d00-0558-11ea-8aad-9e04e1bbff74.png)


![image](https://user-images.githubusercontent.com/45539698/68656775-7536ad80-0558-11ea-8349-e61d1336883b.png)



![image](https://user-images.githubusercontent.com/45539698/68658167-f0995e80-055a-11ea-811a-da960ab9584c.png)



```


```
