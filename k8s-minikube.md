choco install kubernetes-cli

kubectl version --client

Minikube:
-----------------

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb

minikube config set driver docker
minikube start
      or
minikube start --driver vmware

------------------------------------------------

minikube version

minikube help

minikube dashboard

minikube ip

ssh docker@minikubeip 
username :docker
password:tcuser


docker ps


docker is the dafault container runtime for K8s


kubectl cluster-info

kubectl get nodes

kubectl get pods

kubectl get namespaces

minikube status

 kubectl get pods --namespace=kube-system

 kubectl run mynginx --image=nginx

 kubectl get pods

 kubectl describe pod mynginx

 kubectl get pods -o wide

 kubectl dlete pod mynginx

Deployment:
-----------------

kubectl create deployment mynginx-deployment --image=nginx


kubectl get deployments

kubectl get pods

kubectl describe deployment mynginx-deployment

kubectl scale deployment mynginx-deployment --replicas=5

kubectl get pods

kubectl get deployments


Services:
----------------


kubectl expose deployment mynginx-deployment --port=8080 --target-port=80

kubectl get services

default services take clusterIP

if you need to ping you have to login to minicube and ping the service IP

kubectl get svc

kubectl describe services mynginx-deployment

kubectl expose deployment mynginx-deployment --type=NodePort --port=8080

kubectl get svc

minikube ip

http://minikubeip:port 


minikube service mynginx-deployment


App Version Update:
----------------------


kubectl set image deployment mynginx-deployment mynginx-deployment=newimage:2.0

kubectl rollout status deploy mynginx-deployment

succesfully rolled out

minikube dashboard




kubectl get pods


