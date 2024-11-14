## k8s-server

master - oslab
worker - oslab2
\\\bash
# master setting

sudo apt update

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system

sudo sed -i '/swap/d' /etc/fstab

sudo swapoff -a

systemctl stop firewalld

systemctl disable firewalld

CRI 설치

sudo apt-get update

sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

#참고 version 1.28
[https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://v1-28.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

sudo apt-get update

* CRI 설치

sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd/

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl daemon-reload
sudo systemctl start containerd
sudo systemctl enable containerd

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1 
to the file /boot/firmware/cmdline.txt.
sudo kubeadm token  create --print-join-command
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=???? --cri-socket unix:///var/run/containerd/containerd.sock

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

sudo swapoff -a
sudo systemctl restart kubelet

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

이후 join
