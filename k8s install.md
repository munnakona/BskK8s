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