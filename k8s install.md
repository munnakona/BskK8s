curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo -i
# Run these commands as root
###Install GO###
wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
source ~/.bash_profile
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
mkdir bin
go get && go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
usermod -aG docker ubuntu

sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl


 kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket unix:///var/run/cri-dockerd.sock

 ----------------------------------------------------------------

 Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.9.186:6443 --token oc7nyd.g36rl6lycyk15ywl \
        --discovery-token-ca-cert-hash sha256:ef3f2b626d6168b4d2cbb64a044285def92423aa312f37405d49adb31a356815
-----------------------------------------------------------------

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
----------------------------------------------------------

kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

----------------------------------------------------------------------

Node1:
-------------

kubeadm join 172.31.9.186:6443 --token oc7nyd.g36rl6lycyk15ywl \
        --discovery-token-ca-cert-hash sha256:ef3f2b626d6168b4d2cbb64a044285def92423aa312f37405d49adb31a356815

Node2:
--------------

kubeadm join 172.31.9.186:6443 --token oc7nyd.g36rl6lycyk15ywl \
        --discovery-token-ca-cert-hash sha256:ef3f2b626d6168b4d2cbb64a044285def92423aa312f37405d49adb31a356815

---------------------------------------------------------------


kubectl get nodes

kubectl get pods --all-namespaces


kubectl cheatsheet
-----------------

source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell

------------------------------------------------------------------------

Cluster Healthy:
------------------------

kubectl get componentstatuses

--------------------------------------------------------------------

kubectl get nodes

kubectl describe nodes ip-172-31-9-186

kubectl get nodes -o wide
--------------------------------------------------------------------

kube-proxy:

kubectl get daemonsets --namespace=kube-system kube-proxy

----------------------------------------------------------------------------

kuberenetes DNS:

kubectl get deployments --namespace=kube-system coredns

kubectl get services --namespace=kube-system

--------------------------------------------------------------


kubectl api-resources

----------------------------------------------

kubectl apply -f hello-pod.yaml

kubectl describe pod hello-world

kubectl delete -f hello-pod.yml

----------------------------------------------------------

Imperetive :

kubectl run --help
kubectl run hello-pod --image jenkins/jenkins:lts-jdk11
kubectl delete pods hello-pod

docker container run --name mynginx -P -d nginx:1.23.0

----------------------------------------------------------------

Declarative:

---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - image: nginx:1.23.0
      name: my-nginx
      ports:
        - containerPort: 80

------------------------------------------------------------------

In docker to execute the command in the container
execute command:

 docker container exec mynginx pwd


get interactive session:

docker container exec -it mynginx /bin/bash

In k8s also we can do the above
execute command:

kubectl exec nginx-pod -- pwd

get interactive session:

 kubectl exec -it nginx-pod -- /bin/bash

---------------------------------------------------------------------

docker container run --name myalpine -d alpine:3 sleep 1d

---
apiVersion: v1
kind: Pod
metadata:
  name: alpine-pod
spec:
  containers:
    - name: myalpine
      image: alpine:3 
      command: 
        - sleep
        - 1d

        --------------------------------------------------------------



        MYSQL:

        docker container run -d -P -e MYSQL_ROOT_PASSWORD=rootroot --name mysqldb mysql:8


---
apiVersion: v1
kind: Pod
metadata:
  name: mysql-pod
spec:
  containers:
    - name: mysqldb
      image: mysql:8
      ports:
        - containerPort: 3306
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootroot


 ----------------------------------------------------------------------------------------

 init-containers:

 ---
apiVersion: v1
kind: Pod
metadata:
  name: init-containers
spec:
  containers:
    - name: jenkins
      image: jenkins/jenkins:latest
      ports:
        - containerPort: 8080
  initContainers:
    - name: init-api-service
      image: alpine:3
      command: ["sleep", "30s"]
    - name: init-db-service
      image: alpine:3
      command: ["sleep", "30s"]
-------------------------------------------------------------------------------

Requests-lowerLimit

---
apiVersion: v1
kind: Pod
metadata:
  name: resource-requests
spec:
  containers:
    - image: jenkins/jenkins:lts-jdk11
      name: jenkins
      ports:
        - containerPort: 8080
          protocol: TCP
      resources:
        requests:
          cpu: "250m"
          memory: "256Mi"

          ------------------------------------------------------------------------------

          Limits:
---
apiVersion: v1
kind: Pod
metadata:
  name: resource-requests
spec:
  containers:
    - image: jenkins/jenkins:lts-jdk11
      name: jenkins
      ports:
        - containerPort: 8080
          protocol: TCP
      resources:
        limits:
          cpu: "1000m"
          memory: "1000Mi"

-----------------------------------------------------------------------------------------------
Requests & Limits:

---
apiVersion: v1
kind: Pod
metadata:
  name: resource-requests
spec:
  containers:
    - image: jenkins/jenkins:lts-jdk11
      name: jenkins
      ports:
        - containerPort: 8080
          protocol: TCP
      resources:
        requests:
          cpu: "250m"
          memory: "256Mi"
        limits:
          cpu: "1000m"
          memory: "1000Mi"
---------------------------------------------------------------------------------------


Replicas:

kubectl get rs -o wide

kubectl describe rs nginx-rs

kubectl scale replicaset nginx-rs --replicas=4

kubectl get rs

kubectl get pods

--------------------------------------------------------------------------

Daemonsets: pod in 2 nodes


kubectl get ds

kubectl get pods -o wide

kubectl describe daemonsets fluentd

Daemonset: Pod in one node selctor

kubectl label nodes ip-172-16-99-160 ssd=true

---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: specificnodes-ds
  labels:
    app: fluentd
spec:
  minReadySeconds: 10
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
        purpose: logcollection
    spec:
      nodeSelector:
        ssd: "true"
      containers:
        - name: fluentd
          image: fluentd:latest
          ports:
            - containerPort: 24224
--------------------------------------------------------------------------------------------------------

Service:

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
    svc: lb
spec:
  ports:
    - port: 80
      protocol: TCP
  selector:
    app: nginx
    version: "1.23.0"
  type: LoadBalancer

