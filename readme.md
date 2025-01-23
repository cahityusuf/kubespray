# Kubespray Install 

sudo apt update
apt install python3.10-venv
python3 -m venv kubespray-env
sudo apt install -y python3 python3-venv python3-pip libffi-dev libssl-dev build-essential
source kubespray-env/bin/activate
pip install --upgrade pip setuptools wheel
pip install ansible==5.10.0

git clone https://github.com/kubernetes-sigs/kubespray.git
git checkout release-2.27
pip install -r requirements.txt

ssh-keygen
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@controlplane01
ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@controlplane02
ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@loadbalancer
ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@node01
ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@node02
ssh-copy-id -o StrictHostKeyChecking=no $(whoami)@etcd


inventory.ini => configure edilir

########inventory.ini########

[all]
controlplane01 ansible_host=192.168.56.11  ip=192.168.56.11
controlplane02 ansible_host=192.168.56.12  ip=192.168.56.12
node01 ansible_host=192.168.56.21  ip=192.168.56.21
node02 ansible_host=192.168.56.22  ip=192.168.56.22
etcd ansible_host=192.168.56.50  ip=192.168.56.50

[kube_control_plane]
controlplane02
controlplane01

[etcd]
etcd


[kube_node]
node01
node02

[calico_rr]

[k8s_cluster:children]
kube_control_plane
kube_node
calico_rr

########inventory.ini########



ansible-playbook -i /home/vagrant/kubespray/inventory/mycluster/inventory.ini cluster.yml --user=vagrant --ask-pass --become --ask-become-pass -f 3 -vvv