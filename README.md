# Installation

## install jenkins

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

`Note: this may vary for different OS, see: https://docs.docker.com/engine/install/`

```sh
# Add the repository to Apt sources:

sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install the Docker packages.

```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify that the installation is successful by running the hello-world image:

```sh
sudo docker run hello-world
```

======================

# Setup

## Setup jenkins pipeline from git

In Jenkins dashboard

- click on new item--> Name= Package ==> freestyle project
- find source codemanagement, select git, put there https://github.com/jakubmichalko/jenkinsscripts

---

---

---

Setup jenkins for github:

Source Control management:
