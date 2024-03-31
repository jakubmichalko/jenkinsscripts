# Table of Contents

- [Installation](#installation)
  - [Install Jenkins](#install-jenkins)
  - [Install Docker](#install-docker)
    - [Configure Docker](#configure-docker)
  - [Install Kubernetes](#install-kubernetes)
    - [Initiate Kube cluster](#initiate-kube-cluster)
  - [Setup the Calico network](#setup-the-calico-network)
- [Setup](#setup)
  - [Setup Jenkins pipeline from Git](#setup-jenkins-pipeline-from-git)
- [Setup Jenkins for GitHub](#setup-jenkins-for-github)

# Tools Installation

## Install jenkins

`Note: this may vary for different OS, see: https://www.jenkins.io/doc/book/installing/`

Run

```sh
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update

sudo apt-get install jenkins
```

Go to browser:

```sh
localhost:8080
```

Go to the terminal and execute below command:

```sh
cat /var/lib/jenkins/secrets/initialAdminPassword
```

Select -> Install suggested Plugins

Give the username as admin and password at admin

You will be on the dashboard

## Intall docker

```sh
sudo su -
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update ; clear
sudo apt-get install -y docker-ce
```

### config docker

```sh
sudo vi /etc/docker/daemon.json
```

Press i and insert below content

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

Save the file.

```sh
sudo service docker restart
```

Verify that the installation is successful by running the hello-world image:

```sh
sudo docker run hello-world
```

Allow jenkins to run docker.

```sh
sudo chmod 777 /var/run/docker.sock
```

## Intall Kubernetes

```sh
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-get update ; clear
sudo apt-get install -y kubelet kubeadm kubectl
```

#### Initiate Kube cluster

```sh
sudo kubeadm init --ignore-preflight-errors=all
```

This lines are shown in after `sudo kubeadm init ...` command. So run them as well.

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Setup the Calico network

```sh
sudo kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.24.1/manifests/calico.yaml
```

Wait some time and check if Kube Master is "Ready" or not

```sh
kubectl get nodes
```

### Remove tain of the node

Run command

```sh
kubectl describe node
```

and find something like this (you need to scroll a bit):

```
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.31.26.47/20
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.31.128
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 30 Mar 2024 22:45:42 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  ip-172-31-26-47
  AcquireTime:     <unset>
  RenewTime:       Sat, 30 Mar 2024 23:01:34 +0000
Conditions:

```

Use "Taints" and "HolderIdentity" in next command to remove taint:

_Note: Don't forget to append '-' at the end._

```sh
kubectl taint node ip-172-31-26-47  node-role.kubernetes.io/master:NoSchedule-
```

On success should be displayed "untained".

======================

# Setup

## Setup jenkins pipeline from git

In Jenkins dashboard

- click on 'New Item'

  - pick "Pipeline"
  - set iten mame to "WPPipeline"
  - click "OK"

- Scroll down to Definition
- pick "pipeline from SCM"
  - SCM pick "Git"
  - into 'Repository URL" put https://github.com/jakubmichalko/jenkinsscripts.git
  - in "Branches to build", specify "Branch Specifier" to `*/main`
  - Script Path: "Jenkinsfile"
- clik "Save"

---

# Run Deployment

- on Jenkins dasboard, open pipeline WPPipeline, and "Bulild now"
- open terminal:
  - run `kubectl get pods` to check, that all pods have been deployed and started. You should see mysql and wordpress.
  - run `get services`, you should see _mysql_ and _wordpress_.
  - get IP from _wordpress_ service and open it in browser
    - now you should see wordpress application

## https://github.com/jakubmichalko/jenkinsscripts
