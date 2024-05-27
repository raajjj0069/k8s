#!/bin/bash


#requirement ( Ubuntu 24.04 LTS, T2.medium Instance Type) 
#This script automatically setup the minikube on ubuntu 24.04 LTS  
#you juste need to enter below commands in your instance 
#vim script.sh 
#and copy all this content into script.sh file  
#bash script.sh  (note:-run this script using ubuntu user)   

set -e  # Exit immediately if a command exits with a non-zero status

# 1. Installing kubectl
sudo apt update -y
if ! command -v kubectl &> /dev/null; then
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x kubectl
    sudo mv kubectl /usr/local/bin/kubectl
fi
kubectl version --client

# 2. Installing docker
if ! command -v docker &> /dev/null; then
    sudo apt install docker.io -y
    sudo usermod -aG docker ${USER}
    echo " " 
    echo " "   
    echo "*********************************************************************************************"
    echo "Remove the current tab and connect again To your instance "
    echo "We are perfroming this step manually beacause after adding local user into the docker group, "
    echo "We need to refresh the enivironment......." 
    echo "*********************************************************************************************"
    echo " "  
    echo " "   
    exit 1
fi

# 3. Installing Go
if ! command -v go &> /dev/null; then
    wget https://go.dev/dl/go1.22.2.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go
    tar   -xzf go1.22.2.linux-amd64.tar.gz
    sudo  mv go /usr/local/
    export PATH=$PATH:/usr/local/go/bin
fi
go version

# 4. Install cri-dockerd
sudo apt install git -y
git clone https://github.com/Mirantis/cri-dockerd.git
source ~/.profile
cd cri-dockerd
mkdir -p bin
go build -o bin/cri-dockerd
sudo install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
sudo cp -a packaging/systemd/* /etc/systemd/system
sudo sed -i 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
sudo systemctl daemon-reload
sudo systemctl enable cri-docker.service
sudo systemctl enable --now cri-docker.socket

# 5. Install conntrack
sudo apt-get install -y conntrack

# 6. Install crictl
VERSION="v1.24.2"
if ! command -v crictl &> /dev/null; then
    wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz
    sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin
    rm -f crictl-$VERSION-linux-amd64.tar.gz
fi

# 7. Download and Install Minikube
if ! command -v minikube &> /dev/null; then
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    chmod +x minikube
    sudo mv minikube /usr/local/bin/
fi
minikube version

# 8. Starting Minikube
minikube start

# 9. Checking Minikube cluster info
kubectl cluster-info

# 10. cleanup
cd 
rm -rf  go*  
rm  -rf script.sh  
rm  -rf cri-dockerd   