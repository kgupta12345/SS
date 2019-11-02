
###### DEPLOYMENT:
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
###### SERVICE:
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
    app: d

```

###### OBSERVATIONS:

- kubectl get deployment -> a
- kubectl get pods -> a-5cbdc789c7-sjwx4
- kubectl get rs -> a-5cbdc789c7
- kubectl get services ->  g





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
      environment: dev
      tier: backend
    matchExpressions:
      - {key: tier, operator: NotIn, values: [cache]}
      - {key: tier, operator: NotIn, values: [frontend]}
      - {key: tier, operator: In, values: [backend]} 
      - {key: environment, operator: In, values: [dev]}
  template:
    metadata:
      labels:
        tier: backend
        environment: dev
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
    app: d
    #environment: production 
    environment: dev
    tier: backend
 
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
      environment: dev #Optional - Already in ME
      tier: backend #Optional - Already in ME
    matchExpressions:
      - {key: tier, operator: NotIn, values: [cache]}
      - {key: tier, operator: NotIn, values: [frontend]}
      - {key: tier, operator: In, values: [backend]} 
      - {key: environment, operator: In, values: [dev]}
  template:
    metadata:
      labels:
        tier: backend
        environment: dev
        app: d
    spec:
      containers:
      - name: e
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080




NOTE:  All of the requirements, from both matchLabels and matchExpressions are ANDed together – they must all be satisfied in order to match.


> TIER(IN) mention in t-m-l
> ENV(IN) mention in t-m-l


Now Execute:
kubectl get pods -l 'environment in (dev)' -> POD Lists
kubectl get pods -l 'tier notin (cache)’. -> POD Lists

kubectl get pods -l 'app = d' -> POD Lists
kubectl get pods -l 'tier = backend' -> POD Lists
kubectl get pods -l 'environment = dev' -> POD Lists


If you do:

   matchExpressions:
      - {key: tier, operator: NotIn, values: [cache]}
      - {key: environment, operator: In, values: [production]}
  template:

We Get Error:
The Deployment "a" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"d", "environment":"dev"}: `selector` does not match template `labels`
bash-3.2$ kubectl apply -f labseldep.yml

On Doing:
 matchExpressions:
      - {key: tier, operator: NotIn, values: [cache]}
      - {key: environment, operator: In, values: [dev]}
  template:

T-m-l matched & Things Works fine!!!




Now time to make service:

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
    app: d
    #environment: production 
    environment: dev
    tier: backend



If   you would have used environment: production then there would be no matching pod, so no exposure to deployment. On dev, things go right !!!



++++++++++WITHOUT MATCHEXPRESSIONS+++++++++++++


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
      environment: dev
      tier: backend
  template:
    metadata:
      labels:
        tier: backend
        environment: dev
        app: d
    spec:
      containers:
      - name: e
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080

kubectl get pods -l 'environment in (dev)' -> POD Lists
kubectl get pods -l 'environment =dev' -> POD Lists



+++++++++++++++++++++++++++++++++++++++++++++++++



ML—ME ——> WITH RS & RC


RS with ML-ME WORKS FINE

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: appdeploymentfromyml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: appdeploymentfromyml
      environment: dev
      tier: backend
    matchExpressions:
      - {key: tier, operator: NotIn, values: [cache]}
      - {key: tier, operator: NotIn, values: [frontend]}
      - {key: tier, operator: In, values: [backend]} 
      - {key: environment, operator: In, values: [dev]}
  template:
    metadata:
      name: appdeploymentfromyml
      labels:
        app: appdeploymentfromyml
        environment: dev
        tier: backend
    spec:
      containers:
      - name: appdeploymentfromyml
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080


RS without ML-ME PAIR WORKS FINE

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: appdeploymentfromyml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: appdeploymentfromyml
      environment: dev
      tier: backend
  template:
    metadata:
      name: appdeploymentfromyml
      labels:
        app: appdeploymentfromyml
        environment: dev
        tier: backend
    spec:
      containers:
      - name: appdeploymentfromyml
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080 



RC does not have ML definition so if you put ML | ML-ME . The YML DOES NOT COMPILES 
![image](https://user-images.githubusercontent.com/45539698/68065382-75f86400-fd4e-11e9-962e-7b647b51d22f.png)
