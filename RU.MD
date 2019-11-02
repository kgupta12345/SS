### Updating Application in K8
###### Applications are tend to update/upgrade. The below guide aims to address how Applications are updated on Kubernetes.

##### STEP#1: Create a Deployment:
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    kubernetes.io/change-cause: "ImageName: SpringBootDocker"
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
      app: appdeploymentfromyml
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
##### STEP#2: Create a Service:
```

apiVersion: v1
kind: Service
metadata:
  labels:
    app: appservicefromyml
  name: appservicefromyml
  namespace: default
spec:
  type: NodePort
  ports:
  - nodePort: 31746
    port: 8080
    protocol: TCP
    name: http
    targetPort: 8080
  selector:
    app: appdeploymentfromyml

```
###### By default, Kubernetes follows *RollingUpdate* strategy. According to this strategy at a single point of  time a certain number/percentage of pods will be killed and re-created. For example: Let say there are 100 pods and 10% pods are decided for Rolling Update Strategy, then, first 10 pods will be killed and 10 new pods will be re-created....and so on. The process will continue till all the 100 pods are updated. There is another strategy *Recreate* which comes with the cost of downtime. It will shoutdown all replicas and then recreate.

##### NOTE: While the RU is in action, the end user will experience the existing application. After all the pods are recreated, only then, the end user will experience the updated application. Rolling updates allows Deployments to occur with zero downtime by incrementally updating Pods instances with new ones.
 

##### STEP#3: Re-Deploy with Updated image:
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    kubernetes.io/change-cause: "ImageName: SpringBootDocker Version2"
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
      app: appdeploymentfromyml
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
        image: rahulvaish/springbootdocker-v2
        ports:
        - containerPort: 8080

```
##### To understand the above dynamics pictorically: 
###### Follow the series of diagrams below. The Pod with Purple Image is the updated application which needs to be deployed. The Pod with Green Images refers to existing application. </br>
*STEP1 - STEP2* </br>
![image](https://user-images.githubusercontent.com/45539698/68069239-9858a480-fd83-11e9-891d-b4ed0646a6bd.png)
*STEP3 - STEP4* </br>
![image](https://user-images.githubusercontent.com/45539698/68069252-d3f36e80-fd83-11e9-90f6-d51d20f783a7.png)

##### To check the rollout activity 'LIVE' execute:
```
kubectl rollout status deployment appdeploymentfromyml
OR
kubectl get pods -o wide
```






##### Each time we do rollout activity, a new replicaSet is created. To list the Rollout states:
```
kubectl get rs
kubectl rollout history deployment appdeploymentfromyml
kubectl rollout history deployment appdeploymentfromyml --revision 2
```
kubectl rollout undo deployment appdeploymentfromyml --to-revision=3

kubectl rollout pause deployment appdeploymentfromyml
Now on browser what will come ?


kubectl rollout resume deployment appdeploymentfromyml

