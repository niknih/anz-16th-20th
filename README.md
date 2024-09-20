firstdayfeedbck : 

https://forms.office.com/r/zqWcRf3bLU

--------------------

Lastdayfeedbck :

https://forms.office.com/r/QctnvpuYPW

-------------------------

https://etherpad.opendev.org/p/r.79830bb6f4e9595e3b420ed216fd7897


```

Welcome to OpenDev Etherpad!



hi its raman khanna

sdlc :
    
   design >>> code  >> build >> package        >> dockerfile  >>  docker images (read only template )      >>>>  container ( any application )
   
   
   design >>> code  >>      >> dockerfile  >>  docker build to create custom image >> docker images (read only template )      >>>>  container ( any application ) (single hosts /kubenrets cluster )






aws account :

account id : 685421549691

Console sign-in URL
https://685421549691.signin.aws.amazon.com/console

User name

anz-user
Console password

aCV2{'rK


==========================================================

region : california 

3 instances : make sure to name ur instances 


ami : ubuntu

type : t2.medium 

raman-sg

storage : 20 gb



sudo -i
root@ip-172-31-10-31:~# hostnamectl set-hostname master
root@ip-172-31-10-31:~# bash

=======================================================

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/



==================


kubernetes installation using kubeadm ...


1st step : run on all machines ...

1. create a script on each machine

vi script.sh

#!/bin/bash

# Update package lists and install Docker
sudo apt update -y
sudo apt install docker.io -y

# Set up Kubernetes repository and install kubelet, kubeadm, and kubectl
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

# Enable net.bridge.bridge-nf-call-iptables
sudo sysctl net.bridge.bridge-nf-call-iptables=1

# Download and install cri-dockerd
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.14/cri-dockerd-0.3.14.amd64.tgz
tar -xvf cri-dockerd-0.3.14.amd64.tgz
cd cri-dockerd
sudo install -o root -g root -m 0755 cri-dockerd /usr/local/bin/cri-dockerd

# Download and set up cri-dockerd systemd service
cd ..
wget https://github.com/Mirantis/cri-dockerd/archive/refs/tags/v0.3.14.tar.gz
tar -xvf v0.3.14.tar.gz
cd cri-dockerd-0.3.14/
sudo cp packaging/systemd/* /etc/systemd/system
sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service

# Enable and start cri-docker service
sudo systemctl daemon-reload
sudo systemctl enable --now cri-docker.socket
sudo systemctl enable cri-docker
sudo systemctl start cri-docker
sudo systemctl status cri-docker


2. create a config file on the master machine and mention the master node private ip address (advertiseAddress)

vi config.yaml

apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 172.31.3.203
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///var/run/cri-dockerd.sock
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
networking:
  podSubnet: 192.168.0.0/16


kubeadm init --config=config.yaml >> cluster_initialized.txt


------------------------------------------------------------------------------------------------------------------


3. cat cluster_initialized.txt     # run below commands on master


mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config



4. install the calico plugin on master node

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml

5. join the worker nodes using --cri-socket unix:///var/run/cri-dockerd.sock

kubeadm join 172.31.3.203:6443 --token 7gy0n6.gycf0eed7x3lihdj \
        --discovery-token-ca-cert-hash sha256:6dc79151d4cd2c5e1e05e912f7935cf4cae1df149a2f3a74a62d84ee8bbe3cbf \
        --cri-socket unix:///var/run/cri-dockerd.sock





-----------------------------------------------------------------------------------------------


root@master:~# kubectl get nodes
NAME     STATUS   ROLES           AGE    VERSION
master   Ready    control-plane   95m    v1.29.9
w1       Ready    <none>          2m2s   v1.29.9
w2       Ready    <none>          104s   v1.29.9


============================================================



kubectl get nodes
   62  alias k=kubectl
   63  k get pods -A
   64  clear
   65  k get pods -A -o wide
   66  clear
   67  cd ~
   68  alias k=kubectl
   69  k get pods
   70  k get pods -A
   71  k get pods -A -o wide
   72  k get pods -n kube-system
   73  k get pods 
   74  clear
   75  docker ps
   76  kubectl get pods 
   77  kubectl get pods -A
   78  kubectl get pods -A -o wide
   79  clear
   80  kubectl run ramanfirstpod --image httpd
   81  k get pods
   82  k get pods -o wide
   83  192.168.190.65
   84  curl 192.168.190.65
   85  k describe pod ramanfirstpod
   86  clear
   87  k run ramanpod2 --image nginx 
   88  k get pods
   89  k get pods -A
   90  k get pods -A -o wide
 k create ns raman
   96  k get pods -A
   97  k run app3 --image redis -n raman
   98  k get pods
   99  k get pods -n ramana
  100  k get pods -n raman
  101  k get pods -A

==================================================














 k get ns
  115  k get pods
  116  clear
  117  k api-resources
  118  clear
  119  k run app1 --image httpd -n raman
  120  k get pods
  121  k get pods -n raman
  122  pwd
  123  vi pod.yaml
  
  
  
  
  
  
  root@master:~# cat raman/pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: app2
  namespace: raman
spec:
  containers:
  - name: con1
    image: nginx
    ports:
    - containerPort: 80
  - name: con2
    image: redis
    ports:
    - containerPort: 6379   
    
    
    
    
    
  124  k api-reources
  125  k api-resources
  126  clear
  127  vi pod.yaml
  128  ls
  129  k get pods -n raman
  130  k create -f pod.yaml 
  131  k get pods -n raman
  132  k get pods
  133  k delete -f pod.yaml 
  134  vi pod.yaml
  135  vi pod.yaml 
  136  k create pod.yaml 
  137  k create -f pod.yaml 
  138  k get pods -n raman
  139  k delete pods --all
  140  k delete pods --all -n raman
  141  k delete pods -A
  142  k get pods -A
  143  CLEAR
  144  clear
  145  k get pods -n raman
  146  ls
  147  k create -f pod.yaml 
  148  ls
  149  cat pod.yaml 
  150  k get pods -n raman
  151  k get pods -n raman -o wide
  152  k exec -it app2 -n raman -- /bin/bash
  153  k exec -it app2 -n raman -- ls
  154  k exec -it app2 -n raman -- mkdir ramanfolder
  155  k exec -it app2 -n raman -- ls
  156  vi pod.yaml 
  157  k get pods -o wide -n raman
  158  k apply -f pod.yaml 
  159  k get pods -o wide
  160  k get pods -o wide -n raman
  161  k replace -f pod.yaml 
  162  vi pod.yaml 
  163  k delete -f pod.yaml 
  164  k create -f pod.yaml 
  165  k get pods
  166  k get pods -n raman
  167  k describe pod app2
  168  k describe pod app2 -n raman
  169  clear
  170  k get pods
  171  k get pods -n raman
  172  k exec -it app2 -c con2 -- /bin/bash
  173  k exec -it app2 -c con2 -n raman -- /bin/bash
  174  k exec -it app2 -c con1 -n raman -- /bin/bash
  175  k exec -it app2 -c con2 -n raman -- /bin/bash
  176  k exec -it app2 -c con1 -n raman -- hostname
  
  
  
===================================================



code >> dockerfile  >>docker build >>   custom image  / base image   >> pod (containers) 




dockerfile :

# Stage 1: Build Stage
FROM python:3.11-slim AS build

# Build arguments
ARG DEBIAN_FRONTEND=noninteractive

# Set the working directory
WORKDIR /app

# Install curl
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Final Stage
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy installed dependencies from the build stage
COPY --from=build /usr/local/lib/python3.11 /usr/local/lib/python3.11
COPY --from=build /usr/local/bin /usr/local/bin

# Copy the application code
COPY app.py .

# Create a non-root user and switch to it
RUN useradd -ms /bin/sh -u 1001 appuser
USER appuser

# Expose port for the Flask app
EXPOSE 5000

# Set environment variables
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0

# Health check to ensure the application is running
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD curl -f http://localhost:5000/ || exit 1

# Add metadata as described above
LABEL org.opencontainers.image.title="My Flask App" \
      org.opencontainers.image.version="1.0" \
      org.opencontainers.image.description="A simple Flask application" \
      org.opencontainers.image.authors="Your Name <your.email@example.com>"

# Run the Flask application
CMD ["flask", "run"]























=======================================

Deployments :

k api-resources
   35  clear
   36  k create -h
   37  clear
   38  k create deploy dep1 --image ramann123/natwest:my-flask-appV1 --replicas 3
   39  k get all -n raman
   40  k delete deploy --all
   41  k create deploy dep1 --image ramann123/natwest:my-flask-appV1 --replicas 3 -n raman
   42  k get all -n raman
   43  k api-repources
   44  k api-resources
   45  clear
   46  k get pods
   47  k get pods -n raman
   48  clear
   49  k get all -n raman
   50  k get pods -o wide
   51  k get pods -o wide -n raman
   52  curl 192.168.80.197:5000

clear
   54  history
   55  clear
   56  k get all -n raman
   57  k delete pod dep1-8469884bc8-d4fvs
   58  k delete pod dep1-8469884bc8-d4fvs -n raman
   59  k get pods -n raman
   60  k get pods -n raman -o wide
   61  clear
   62  k get all
   63  clear
   64  k get all -n raman
   65  k describe pod dep1-8469884bc8-9x9r5
   66  k describe pod dep1-8469884bc8-9x9r5 -n raman
   67  clear
   68  k get deploy --all -n raman
   69  k get deploy -n raman
   70  k describe deploy dep1 -n raman
   71  clear
   72  k get pods -n raman
   73  k describe pod 8469884bc8-9x9r5 | grep -i label
   74  k describe pod 8469884bc8-9x9r5 -n raman | grep -i label
   75  k describe pod dep1-8469884bc8-9x9r5 -n raman | grep -i label
   76  k get rs -n raman
   77  k describe rs dep1-8469884bc8  -n raman | grep -i selector
   78  k describe rs dep1  -n raman | grep -i selector
   79  k describe deploy dep1  -n raman | grep -i selector
   80  k describe rs dep1-8469884bc -n raman | grep -i label
   81  k describe deploy  dep1 -n raman | grep -i selector


==================================================================

DELETE ALL DEPLOY AND PODS AFTER PRACTISE LAB , 



root@master:~# cat deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
  namespace: raman
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flaskcon
        image: ramann123/natwest:my-flask-appV1
        ports:
        - containerPort: 5000






k get pods
  160  k get pods -n raman
  161  clear
  162  k get all -n raman
  163  k describe deploy flask-deployment | grep -i selector
  164  k describe deploy flask-deployment -n raman| grep -i selector
  165  k describe rs flask-deployment-6c4c8f7594  -n raman| grep -i selector
  166  k describe pods  -n raman| grep -i label
  167  k get pods -o wide -n raman
  168  curl 192.168.190.74
  169  curl 192.168.190.74:5000
  170  k run myapptest --image httpd
  171  k describe pod myapptest | grep -i label
  172  k label pod myapptest "name=raman"
  173  k describe pod myapptest | grep -i label
  174  k get pods --selector "name=raman"
  175  k get pods --selector "app=flask"
  176  k get pods --selector "app=flask" -n raman
  177  k label pod myapptest "app=flask"
  178  k describe pod myapptest | grep -i label
  179  k get pods --selector "app=flask"
  180  k get pods --selector "app=flask" -A
  181  clear
  182  ls
  183  cat deploy.yaml 
  

  k scale deploy dep1 --replicas 4
  
  ====================================================
  
  SERVICES :
      
      
      
k describe pod myapptest | grep -i image
  277  k get pods -o wide
  278  curl 192.168.80.205
  279  k get svc
  280  k expose pod myapptest --name httpdsvc --type NodePort --port 80 --target-port 80
  281  k get svc
  282  k describe svc httpdsvc
  283  k get svc
  284  k get pods
  285  k get svc
  
  
  k expose deploy dep1 --name httpdsvc2 --type NodePort --port 80 --target-port 80
  



k expose deploy myapptest --name httpdsvc --type LoadBalancer --port 80 --target-port 80

cluster ip service



===============================================================================
https://github.com/ramannkhanna2/kubernetes_end2end_front-backend.git





============================================

Horizontal Pod Autoscaler

kubectl get hpa
kubectl autoscale deployment dep1 --cpu-percent=70 --min=1 --max=5
kubectl get hpa
kubectl get pods
kubectl get hpa

git clone https://github.com/ramannkhanna2/k8s_metrics_server.git

k apply -f .

k top nodes
k top pods
kubectl get hpa
kubectl get pods
kubectl get pods -A
kubectl get hpa
kubectl describe hpa
edit the deployment and make sure to add cpu request as well under container specifications
watch kubectl get hpa
to increase the load go to :

#to increase the load , get inside the httpd container 
kubectl exec -it "podname" -- /bin/bash
apt update -y ( around 6-7 times)
kubectl delete hpa dep1


-------------


root@master:~/k8s_metrics_server/deploy/1.8+# cat dep2.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: centos-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: centos
  template:
    metadata:
      labels:
        app: centos
    spec:
      containers:
      - name: centos-container
        image: ubuntu
        command: ["sleep", "infinity"]
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"



k apply -f deploy2.yaml

alias k=kubectl
 1028  k get pods
 1029  kubectl autoscale deployment centos-deployment --cpu-percent=70 --min=1 --max=5
 1030  k get hpa
 1031  history
 1032  clear
 1033  k get pods
 1034  k get hpa
 1035  k get hpa --watch'
 1036  k get hpa --watch


https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

===========================================================================================



kubectl apply -f deploy3.yaml --record=true
 1056  kubectl rollout status deployment raman-app
 1057  k get pods
 1058  clear
 1059  k get deploy
 1060  k describe deploy ramana-app | grep -i image
 1061  k describe deploy raman-app | grep -i image
 1062  k get rs
 1063  k get pods
 1064  kubectl rollout history deployment raman-app
 1065  vi deploy3.yaml 
 1066  kubectl apply -f deploy3.yaml --record=true
 1067  kubectl rollout status deployment raman-app
 1068  k get pods
 1069  k get deploy
 1070  k get rs
 1071  kubectl rollout history deployment raman-app
 1072  k describe deploy ramana-app | grep -i image
 1073  k describe deploy raman-app | grep -i image
 1074  vi deploy3.yaml 
 1075  kubectl apply -f deploy3.yaml --record=true
 1076  kubectl rollout history deployment raman-app
 1077  kubectl rollout status deployment raman-app
 1078  clear
 1079  kubectl rollout history deployment raman-app
 1080  k get pods
 1081  k describe deploy raman-app | grep -i image
 1082  k get rs
 1083  kubectl rollout undo deployment raman-app --to-revision=1
 1084  kubectl rollout status deployment raman-app
 1085  clear
 1086  ls
 1087  k describe deploy raman-app | grep -i image
 1088  k get rs







root@tech-master:/gagan# cat deploy.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gagan-app
  labels:
    app: web2-prod-app
spec:
  replicas: 50
  strategy:
    type: Recreate
#    type: RollingUpdate
#    rollingUpdate:
#      maxSurge: 25%
#      maxUnavailable: 25%
  selector:
    matchLabels:
      app: web2-prod-app
  template:
    metadata:
      labels:
        app: web2-prod-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80







---- 
revision :1 nginx: latest
raman-app : deploy
raman-app-5466fcd5b5 : replicaset





revision2 : nginx:1.14
raman-app : deploy
raman-app-7566b7d5cd : replicaset




revision 3 : nginx:1.14.1
raman-app-6fd86f7b8f 
deploy : raman-app





=======================================================




echo 'ramankhanna123' | base64
 1127  echo 'ramankhanna' | base64
 1128  vi secret.yaml
 1129  cat secret.yaml 
 1130  k get secrets
 1131  k apply -f secret.yaml 
 1132  k get secrets
 1133  k describe secret
 1134  ls
 1135  vi pod4.yaml
 1136  cat pod4.yaml 
 1137  k create -f pod4.yaml 
 1138  k get secrets
 1139  k describe pod 
 1140  k get pods
 1141  k exec -it myapp-pod1 -- /bin/bash
 1142  clear
 1143  vi pod5.y
 1144  vi pod5.yaml
 1145  k create -f pod5.yaml 
 1146  k get pods
 1147  k exec -it myapp-pod2
 1148  k exec -it myapp-pod2 -- /bin/bash
 1149  k create secret -h
 1150  clear
 1151  history






 encrypt the data first of all
password :
   echo 'ramankhanna123' | base64

username :
   echo 'ramankhanna' | base64

  462  cat secret.yaml
  463  kubectl apply -f secret.yaml
  464  kubectl get secret
  465  kubectl describe secret my-secrets
  466  kubectl describe secret my-secrets -o yaml
  467  vi secret-pod.yaml
  468  kubectl apply -f secret
  469  kubectl apply -f secret-pod.yaml
  470  kubectl get pods
  471  kubectl exec -it myapp-pod1 -- /bin/bash
  472  kubectl exec -it myapp-pod2 -- /bin/bash
     #to access username and password as env variables in pod2 :
       echo $SECRET_USERNAME
       echo $SECRET_PASSWD

  473  cat secret-pod.yaml
  474  cat secret.yaml
  475  history
root@tech-master:/gagan# cat secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secrets
type: Opaque
data:
  username: cmFtYW5raGFubmEK
  password: cmFtYW5raGFubmExMjMK
root@tech-master:/gagan# cat secret-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod1
  labels:
    app: myapp
spec:
  containers:
  - name: httpd-container
    image: httpd
    volumeMounts:
    - name: credentials
      mountPath: /tmp/creds
      readOnly: true
  volumes:
  - name: credentials
    secret:
      secretName: my-secrets

---
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod2
  labels:
    app: myapp
    type: front-end
spec:
  containers:
  - name: httpd-container
    image: httpd
    env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secrets
          key: username
    - name: SECRET_PASSWD
      valueFrom:
        secretKeyRef:
          name: my-secrets
          key: password




====================================================================



CONFIGMAPS:

echo "Hello from production" > prod.html
echo "Hello from dev" > dev.html
kubectl get configmaps
kubectl create configmap prod.cmap --from-file=prod.html
kubectl create configmap dev.cmap --from-file=dev.html
kubectl get configmaps
kubectl get configmap dev.cmap -o yaml
kubectl get configmap prod.cmap -o yaml
vi pods.yaml

----

root@gagan-master:/gagan/cmap# cat pods.yaml
apiVersion: v1
kind: Pod
metadata:
  name: prod-nginx
  labels:
    app: prod-nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: config-volume
      mountPath: /usr/share/nginx/html
  volumes:
    - name: config-volume
      configMap:
        name: prod.cmap
        items:
        - key: prod.html
          path: index.html

---
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx
  labels:
    app: dev-nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: config-volume
      mountPath: /usr/share/nginx/html
  volumes:
    - name: config-volume
      configMap:
        name: dev.cmap
        items:
        - key: dev.html
          path: index.html


root@gagan-master:/gagan/cmap#

===================================================
PV/ PVC


root@master:~# cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redispod
  namespace: default
spec:
  restartPolicy: Never
  volumes:
  - name: vol
    hostPath:
      path: /home/ubuntu
  containers:
  - name: redis
    image: "redis"
    volumeMounts:
    - name: vol
      mountPath: /hostfile
  nodeName: worker2


#Create a pod without using nodename parameter volume being mapped to hostvolume
#check the host directory on my worker on whoch pod is running for the volumes
#create second pod of same kind using nodename parameter on worker2 
exec inside the container and then check the same vol is created on worker2 as well
so volume is replicated thats why we use pv pvc in kubernetes

----


apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-website
spec:
  capacity:
    storage: 11Mi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /
    server: 172.31.12.142

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-demo
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Mi
  volumeName: nfs-website

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-raman
spec:
  replicas: 10
  selector:
    matchLabels:
      role: nfs-raman
  template:
    metadata:
      labels:
        role: nfs-raman
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nfs
          mountPath: /usr/share/nginx/deploydata
      volumes:
      - name: nfs
        persistentVolumeClaim:
          claimName: nfs-demo

---
kubectl expose deploy nfs-raman --name nfs-svc --type NodePort --target-port 80 --port 80


 kubectl get pv
 kubectl get pods

 note : make sure to check security group of mount target if traffic opened or not

 apt install -y nfs-server       or    apt-get install nfs-common    (ubuntu)
 mkdir /efs
 cd /
 sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 172.31.12.142:/ efs
 df -h
 cd /efs
 ls -l

** Browse : publicip:port asisgned


k get pods
 1167  k get pods -o wide
 1168  cat pod6.yaml 
 1169  k exec -it redispod -- /bin/bash
 1170  clear
 1171  ls
 1172  k get pv
 1173  k get pvc
 1174  vi pv.yaml
 1175  cat pv.yaml 
 1176  vi pv.yaml 
 1177  mkdir /efs
 1178  ls
 1179  cd /
 1180  ls
 1181  sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0892aa98321f4adbe.efs.us-west-1.amazonaws.com:/ efs
 1182  cd efs/
 1183  ls
 1184  rm -rf anz 
 1185  cd ..
 1186  ls
 1187  cd /root/
 1188  ls
 1189  vi pv.yaml 
 1190  k apply -f pv.yaml 
 1191  k get pv
 1192  df -h
 1193  clear
 1194  k get pv
 1195  vi pvc.yaml
 1196  k apply -f pvc.yaml 
 1197  k get pvc
 1198  k describe pvc
 1199  clear
 1200  ls
 1201  vi deploy4.yaml
 1202  k create deploy4.yaml 
 1203  k create -f deploy4.yaml 
 1204  k get pods -o wide
 1205  kubectl expose deploy nfs-raman --name nfs-svc --type NodePort --target-port 80 --port 80
 1206  k get svc
 1207  k exec -it nfs-raman-7967c5558d-282ll -- /bin/bash
 1208  k exec -it nfs-raman-7967c5558d-b2w7c -- /bin/bash
 1209  history
 1210  vi pv.yaml 
 1211  k apply -f pv.yaml 
 1212  k get pv
 1213  k get pvc
 1214  k get pods
 1215  history
 
 
 
 ===============================================================================================================


Health-checks :

-----------------------------------
Liveliness probe :

-- start with incorrect cmnds for all probes , then rectify them ..
-- this probe restarts the pod.


root@master:~# cat liveliness.yml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:4.0.8
          livenessProbe:
            exec:
              command:
                - mongo
                - --eval
              # - "db.adminCommand('ping')"
                - "db1.adminCommand('ping')" 
            initialDelaySeconds: 1
            periodSeconds: 10
            timeoutSeconds: 5
            successThreshold: 1
            failureThreshold: 2


----------------------------

Readiness probe :

-- start from incorret command 
-- this removes pod from svc endpoint .


root@master:~# cat readiness.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:4.0.8
          readinessProbe:
            exec:
              command:
                - mongo
                - --eval
              #  - "db.adminCommand('ping')"
                - "db1.adminCommand('ping')" 
            initialDelaySeconds: 1
            periodSeconds: 10
            timeoutSeconds: 5
            successThreshold: 1
            failureThreshold: 2
---
apiVersion: v1
kind: Service
metadata:
  name:  mongodb-service
spec:
  selector:
    app: mongo
  ports:
  - protocol:  TCP
    port:  27017
    targetPort:  27017


============================================================


ubuntu ,
t2.micro
8-10 gb


install tf ....

https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli

====================================================================



==========================================

https://github.com/hashicorp/terraform-provider-aws/releases







=====================================================


root@ip-172-31-24-177:~# cat main.tf 
provider "aws" {
  region     = "us-west-1"
#  version    = "2.7"
  access_key = "AKIAZ7FSO3B5SQQKBSHW"
  secret_key = "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"
}


resource "aws_instance" "rk" {
  ami           = "ami-0d53d72369335a9d6"
  instance_type = "t2.micro"
  tags = {
    Name = "raman-HelloWorld"
  }
}



 1  sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
    2  wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
    3  gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint
    4  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    5  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    6  sudo apt update
    7  sudo apt-get install terraform
    8  terraform 
    9  clear
   10  terraform --version
   11  clear
   12  vi main.tf
   13  cat main.tf 
   14  vi main.tf 
   15  alias t=terraform
   16  t init
   17  cat main.tf 
   18  vi main.tf 
   19  ls -a
   20  cd .terraform
   21  ls
   22  cd providers/
   23  ls
   24  cd registry.terraform.io/
   25  ls
   26  cd hashicorp/
   27  ls
   28  cd aws/
   29  ls
   30  cd 5.67.0/`
   31  cd 5.67.0/
   32  ls
   33  cd linux_amd64/
   34  ls
   35  cd /root/
   36  ls
   37  terraform --version
   38  t -h
   39  clear
   40  history
   41  ls
   42  ls -a
   43  ls
   44  t validate
   45  t plan
   46  t apply 
   47  cat main.tf 
   48  ls
   49  t state list
   50  cat terraform.tfstate 
   51  cat terraform.tfstate | grep -i Nmae
   52  cat terraform.tfstate | grep -i Name
   53  cat main.tf 
   54  t cat main.tf 
   55  cat main.tf 
   56  t refresh
   57  cat terraform.tfstate | grep -i Name
   58  cat main.tf 
   59  t plan
   60  cat main.tf 
   61  t apply
   62  t destroyt destroy
   63  t destroy
   64  cat main.tf 
   65  history
   
   
   =========================================
   
   
   
   

Lab: Desired ,current state and last known configuration :

-- create an ec2 instance using iac and t2.micro

-- check the management console for the current state of the instance

-- change the instance type to t2.nano from management console

--- now check the tfstate file , are there any changes ?

-- terraform refresh or terraform plan to scan the env and update the tf state file

---now check the tfstate file agn to see if there is any change in last knownconfiguration

--- now again do terraform applywith t2. micro still in iac file

*************************************************************************


CHALLENGE WITH DESIRED AND CURRENT STATE :                  :

---  apply the firstec2.tf again and create a server.

--- create custom security group and replace the default security group with it in instance through mngemnt console.

---- terraform refresh to update tf state file

---- check the tfstate file

--- now do terraform apply and see if it chnges bck to default security group

--- we will notice it doesnt changes becoz we have not mentioned the security groups resource block in our iac which is the desired state.


====================================================================
   
   
   
   
   
   root@ip-172-31-24-177:~# cat azure.tf 
provider "azurerm" {}

root@ip-172-31-24-177:~# cat git.tf 
terraform {
  required_providers {
    github = {
      source  = "integrations/github"
      version = "~> 6.0"
    }
  }
}

# Configure the GitHub Provider
provider "github" {
token = ""
}

resource "github_repository" "example" {
  name        = "raman-test"
  description = "My awesome codebase"

  visibility = "public"
}




=================================================================






DEPLOYMENTS :

```
root@ip-172-31-44-195:~# cat main.tf 
provider "kubernetes" {
  config_path = "config"
}

resource "kubernetes_namespace" "raman" {
  metadata {
    name = "raman1"
  }
}


resource "kubernetes_deployment" "dep1" {
  metadata {
    name      = "dep1"
    namespace = kubernetes_namespace.raman.metadata[0].name
  }

  spec {
    replicas = 3
    strategy {
      type = "Recreate"  # Use Recreate strategy
      # Uncomment below for RollingUpdate strategy
      # type = "RollingUpdate"
      # rolling_update {
      #   max_surge = "25%"
      #   max_unavailable = "25%"
      # }
    }    

    selector {
      match_labels = {
        app = "dep1"
      }
    }

    template {
      metadata {
        labels = {
          app = "dep1"
        }
      }

      spec {
        container {
          name  = "my-flask-app"
          image = "ramann123/natwest:my-flask-appV1"
          port {
            container_port = 5000
          }
          resources {
            requests = {
              cpu    = "250m"
              memory = "64Mi"
            }
            limits = {
              cpu    = "500m"
              memory = "128Mi"
            }
          }
        }
      }
    }
  }
}

==============================================================================================

SERVICE :

root@ip-172-31-44-195:~# cat svc.tf 
resource "kubernetes_service" "dep1" {
  metadata {
    name      = "dep1-service"
    namespace = kubernetes_namespace.raman.metadata[0].name
  }

  spec {
    type = "NodePort"  # Set service type to NodePort

    selector = {
      app = "dep1"
    }

    port {
      protocol    = "TCP"
      port        = 5000       # Service port
      target_port = 5000     # Container port
      node_port   = 30000    # NodePort port (must be within the range 30000-32767)
    }
  }
}




===============================================================================



LOCAL PV/PVC :

root@ip-172-31-44-195:~# cat pv.tf 
resource "kubernetes_persistent_volume" "local_pv" {
  metadata {
    name = "local-pv"
  }

  spec {
    capacity = {
      storage = "10Gi"
    }

    access_modes = ["ReadWriteMany"]

    persistent_volume_source {
      local {
        path = "/mnt/disks/v1"
      }
      }

    node_affinity {
      required {
        node_selector_term {
          match_expressions {
            key      = "kubernetes.io/hostname"
            operator = "In"
            values  = ["w1"]  # Replace with the actual node name
          }
        }
      }
    }
  }
}






root@ip-172-31-44-195:~# cat pvc.tf 
resource "kubernetes_persistent_volume_claim" "local_pvc" {
  metadata {
    name = "local-pvc"
    namespace= "raman1"
  }
  spec {
    access_modes = ["ReadWriteMany"]
    resources {
      requests = {
        storage = "5Gi"
      }
    }
    volume_name = kubernetes_persistent_volume.local_pv.metadata[0].name
  }
}







root@ip-172-31-44-195:~# cat depPvPvc.tf 
resource "kubernetes_deployment" "deployment" {
  metadata {
    namespace= "raman1"
    name = "deployment"
  }

  spec {
    replicas = 3

    selector {
      match_labels = {
        app = "deployment"
      }
    }

    template {
      metadata {
        labels = {
          app = "deployment"
        }
      }

      spec {
        container {
          name  = "nginx"
          image = "nginx"

          volume_mount {
            name      = "vol"
            mount_path = "/usr/share/nginx/deploydata"
          }
        }

        volume {
          name = "vol"

          persistent_volume_claim {
            claim_name = kubernetes_persistent_volume_claim.local_pvc.metadata[0].name
          }
        }
      }
    }
  }
}





===============================================================

root@ip-172-31-24-177:~# cat user.tf 
provider "aws" {
  region     = "us-west-1"
#  version    = "2.7"
  access_key = "3"
  secret_key = "
}

resource "aws_iam_user" "lb" {
  name = "iamuser.${count.index}"
  count = 3
  path = "/system/"
}

output "arns" {
  value = aws_iam_user.lb[*].arn
}



==============================================================



Variables :
    
    
    
    root@ip-172-31-24-177:~# cat sg.tf 
provider "aws" {
  region     = "us-west-1"
#  version    = "2.7"
  access_key = "AKIAZ7FSO3B54KM6YN63"
  secret_key = "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"
}

resource "aws_security_group" "var_demo" {
  name        = "raman-variables"
  vpc_id      = "vpc-07a1a305b7234d188"

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = [var.rk]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = [var.rk]
  }

  ingress {
    from_port   = 53
    to_port     = 53
    protocol    = "tcp"
    cidr_blocks = [var.rk]
  }
}
root@ip-172-31-24-177:~# cat variable.tf 
variable "rk" {
#  default = "116.50.30.70/32"
   default = "0.0.0.0/0"
}




====================================================================





root@ip-172-31-24-177:~# ls
infrastructure.json  kubernetes  main.tf  prod.tfvars  sg.tf  snap  terraform.tfstate  terraform.tfstate.backup  user  variable.tf
root@ip-172-31-24-177:~# cat main.tf n
provider "aws" {
  region     = "us-west-1"
#  version    = "2.7"
  access_key = "AKIAZ7FSO3B54KM6YN63"
  secret_key = "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"
}


resource "aws_instance" "myawsserver" {
  ami = "ami-0d53d72369335a9d6"
  instance_type = var.type

  tags = {
    Name = "anz-aws-ec2-instance"
  }
}
cat: n: No such file or directory
root@ip-172-31-24-177:~# cat variable.tf 
variable "rk" {
#  default = "116.50.30.70/32"
   default = "0.0.0.0/0"
}

variable "type" {
default = "t2.micro"
}
root@ip-172-31-24-177:~# cat prod.tfvars 
type="t2.medium"
rk="116.50.30.70/32"


========================




LAB : count parameter and indexes :

---To create muktiple resources at once :

count-paremeter.tf
provider "aws" {
  region     = "us-east-2"
  access_key = "YOUR-ACCESS-KEY"
  secret_key = "YOUR-SECRET-KEY"
}


resource "aws_instance" "instance-1" {
   ami = "ami-064ff912f78e3e561"
   instance_type = "t2.micro"
   count = 3
}

--- to create multiple resources with specific indexes:

provider "aws" {
  region     = "us-east-2"
}


resource "aws_iam_user" "lb" {
  name = "loadbalancer${count.index}"
  count = 3
}


--- To create multiple resources with different names :

1st way:

provider "aws" {
  region     = "us-east-2"
}

variable "elb-names" {
  type = list
  default = ["dev-loadbalancer", "stage-loadbalanacer","prod-loadbalancer"]
}

resource "aws_iam_user" "lb" {
  name = var.elb-names[count.index]
  count = 3
}


resource "aws_instance" "s1" {
instance_type= "t2.micro"
ami= "ami-064ff912f78e3e561"
count= 3
tags= {
Name= var.elb-names[count.index]
}
}


2nd way :

[root@ip-172-31-87-20 app1]# cat iam.tf
provider "aws" {
 region = "us-east-1"

}


resource "aws_iam_user" "user" {
  name = "${var.elb-names[count.index]}"
  count = 3
}


[root@ip-172-31-87-20 app1]# cat variable.tf
variable "elb-names"{
 default = ["dev-lb","prod-lb","stage-lb"]
}




=================================================================


HELM INTEGRATION :


root@ip-172-31-44-195:~/helm# ls
config  helm.tf  terraform.tfstate  terraform.tfstate.backup  values.yaml




root@ip-172-31-44-195:~/helm# cat helm.tf 
# Define the Helm provider
provider "helm" {
  kubernetes {
    config_path = "config"  # Adjust path as needed
  }
}


provider "kubernetes" {
  config_path = "config"  # Adjust the path as needed
}

# Define the namespace (create it if it does not exist)
resource "kubernetes_namespace" "example" {
  metadata {
    name = "rk1-namespace"
  }
}

# Define the Helm release resource with namespace
resource "helm_release" "nginx" {
  name       = "nginx-release"
  repository = "https://charts.bitnami.com/bitnami"
  chart      = "nginx"
  version    = "15.2.0"  # Specify the chart version you want to use

  namespace = kubernetes_namespace.example.metadata[0].name

  set {
    name  = "service.type"
    value = "NodePort"
  }

  set {
    name  = "replicaCount"
    value = "3"
  }

  values = [file("values.yaml")]
}











root@ip-172-31-44-195:~/helm# cat values.yaml 
# General settings
name: nginx
namespace: default
replicaCount: 2

# Service configuration
service:
  enabled: true
  name: nginx-service
  type: LoadBalancer  # Options: ClusterIP, NodePort, LoadBalancer
  port: 80
  targetPort:
    http: 80  # This should match the chart's expected value

# Resources configuration
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
  requests:
    cpu: "250m"
    memory: "256Mi"

# Image configuration
image:
  repository: bitnami/nginx
  tag: latest
  pullPolicy: IfNotPresent

# Service account configuration
serviceAccount:
  create: true
  name: ""


=====================================================================


https://developer.hashicorp.com/terraform/language/functions

read : 1 week


https://www.examtopics.com/exams/hashicorp/terraform-associate/view/


======================================================================

nodejsapp_jenkins-docker-kubernetes PublicForked from shazforiot/nodeapp_test

k8s-logging-efk

Devops-terraform-ansible 


kubernetes_end2end_front-backend

==================================================================================

```
