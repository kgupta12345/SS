PVC





======================================
vi persistentVolumeClaim.yml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

Kubectl apply -f persistentVolumeClaim.yml

======================================

vi pvc-deployment.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: pv-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mypv
    spec:
      containers:
      - name: shell
        image: rahulvaish/springbootdocker
        volumeMounts:
        - name: mypd
          mountPath: "/Users/rvaish/KUBEExperiments/“
      volumes:
      - name: mypd
        persistentVolumeClaim:
          claimName: my-pvc

Kubectl apply -f pvc-deployment.yml

======================================

vi pvc-deploymentv2.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: pv-deploy-v2
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mypvv2
    spec:
      containers:
      - name: shell
        image: rahulvaish/springbootdocker-v2
        volumeMounts:
        - name: mypdv2
          mountPath: "/Users/rvaish/KUBEExperiments/“
      volumes:
      - name: mypdv2
        persistentVolumeClaim:
          claimName: my-pvc

Kubectl apply -f pvc-deploymentv2.yml

======================================

kubectl get pv,pvc
Pv and pvc lists

Go inside pod1 and create a  file and add a text.
Go inside another pod - pod2  and navigate till that location and you will see the file with data.
Delete deployment1 or pod1.
Recreate deployment1 or pod1.

Go inside the new pod from new redeployment of deployment1 and and you will see that  the file and its contents are present like before :)

WOW, data persisted beyond pod lifecycle :D
+ Data can be shares between pods.


=================================================================


K8 offers various supports to connect with different kind of volumes - azure/aws(cloud),emptydir/hostpath (mount based) and nfs(network based). Volumes are created by admins.
 


EMPTYDIR:


vi emptydir.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: vol-ed
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: vol-ed
    spec:
      containers:
      - name: shell
        image: rahulvaish/springbootdocker-v2
        volumeMounts:
        - name: vol-ed
          mountPath: "/Users/rvaish/KUBEExperiments/"
      volumes:
      - name: vol-ed
        emptyDir: {}

Kubectl apply -f emptydir.yml

Go inside pod
Create a folder
Delete the pod
Go inside the new pod
Do ls
No folder listed.


HOSTPATH:

vi hostpath.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: vol-hp
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: vol-hp
    spec:
      containers:
      - name: shell
        image: rahulvaish/springbootdocker-v2
        volumeMounts:
        - name: vol-hp
          mountPath: "/mnt-vol”
      volumes:
      - name: vol-hp
        hostPath:
            path: “/Users/rvaish/KUBEExperiments/volume-on-machine”

Kubectl apply -f hostpath.yml


Goto System’s /Users/rvaish/KUBEExperiments/
You will find volume-on-machine
Create a file  inside /Users/rvaish/KUBEExperiments/volume-on-machine as volumeonmachine.txt with some data

Go inside pod
Goto /mnt-vol
Ls
You will find volumeonmachine.txt
Create a folder there mntfolder


Now, again Goto /Users/rvaish/KUBEExperiments/volume-on-machine/ 
bash-3.2$ ls
mntfolder		volumeonmachine.txt
bash-3.2$ 


<OPEN QUESTION EXPERIMENT ON 2 NODES [PENDING DUE TO INFRA| THEORY AS BELOW] >

Here pod went on node2 therefore volume-on-machine is made let say on node2. 


SITUATION ON NODE2 WILL BE:
Now, again Goto /Users/rvaish/KUBEExperiments/volume-on-machine/ 
bash-3.2$ ls
mntfolder		volumeonmachine.txt
bash-3.2$ 



Next time the pod goes on node1 . Now volume-on-machine _______________________________. 
Navigating inside pod there you will see __________________________________________________.





=================================================================







GCK (on Thinkpad)

=================================================================

PV PVC
(>|*|*|*|*|*|*|*|*|*|<ABOVE EXAMPLE WAS ON DYAMIC PROVISIONING>|*|*|*|*|*|*|*|*|*|<)
(PV gets created at the same time of PVC is Dynamic Provisioning)
(Below example tells about Static Provisioning where PV is created before PVC)


vi pvhp.yml

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
    path: "/pvhp/data”

Kubectl apply -f pvhp.yml





—————————————



vi pvchp.yml

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


Kubectl apply -f pvchp.yml







vi pvdep.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: pvdep
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: pvdep
    spec:
      containers:
      - name: shell
        image: rahulvaish/springbootdocker
        volumeMounts:
        - name: pvdep
          mountPath: "/mnt-pvdep/"
      volumes:
      - name: pvdep
        persistentVolumeClaim:
            claimName: pvchp
	

Kubectl apply -f pvdep.yml



<OPEN QUESTION - WHERE DOES THE PV ("/pvhp/data”) GETS CREATED>

Go inside pod 
You will find /mnt-pvdep/
Create a folder
Delete the dployment
Re-instantiate the deployment
Go inside the pod
You will find the  folder
=================================================================




=================================================================

DELETION: 
kubectl delete -n default persistentvolumeclaim <pvc name>
pvc then pv gets deleted 

Q: Can we deleted pv and check for pvc status?
Ans: It will Hang on “Termination State”, follow the below steps:
kubectl get pv
kubectl edit persistentvolume/<PV NAME>
Now delete the two lines that refer to its finalizers. It looks something like
finalizers:
  -  kubernetes.io/pv-protection

Now, kubectl get pv
No resources found.
Now, kubectl get pvc
“Lost”
Then you use to kubectl delete pvc <PVC name>
Deleted :)


=================================================================

