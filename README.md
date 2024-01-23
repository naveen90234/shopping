
# Shopping App with DevSecOps

In this project we will use : Git, Github, Jenkins, SonarQube, Trivy, Owasp, K8s And Argocd


![App Screenshot](https://raw.githubusercontent.com/jeetu844/screenShots/main/Shopping-reactJS-DevOps/Jenkins_pipeline.png)
### In this project we are using 3 servers which OS is CentOS 7
- **1st Server** (142.44.249.117)
  - [Jenkins](https://github.com/jeetu844/Shopping-reactJS-DevOps#jenkins-installation)
  - [Docker](https://github.com/jeetu844/Shopping-reactJS-DevOps#docker-installation)
  - [SonarQube](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#sonarqube-installation)
  - [Trivy](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#trivy-installation)
- **2nd Server** (142.44.249.127)
  - [Docker](https://github.com/jeetu844/Shopping-reactJS-DevOps#docker-installation)
  - [Kubernetes Master](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#k8s-installation)
  - [Argocd](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#argocd-installation)
  - [Helm](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#prometheus-grafana-installation-on-k8s)
  - [Prometheus](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#prometheus-grafana-installation-on-k8s)
  - [Grafana](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#prometheus-grafana-installation-on-k8s)
- **3rd Server** (142.44.249.126)
  - [Docker](https://github.com/jeetu844/Shopping-reactJS-DevOps#docker-installation)
  - [Kubernetes Node](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#k8s-installation)

## Installation Steps

- Step1: [Jenkins Installation on CentOS 7](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#jenkins-installation)
- Step2: [Docker Installation on CentOS 7](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#docker-installation)
- Step3: [SonarQube Installation using Docker Image](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#sonarqube-installation)
- Step4: [Trivy Installation on CentOS 7](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#trivy-installation)
- Step5: [K8s Installation on CentOS 7](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#k8s-installation)
- Step6: [Argocd Installation on K8s](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#argocd-installation)
- Step7: [Prometheus Grafana Installation on K8s](https://github.com/jeetu844/Shopping-reactJS-DevOps#prometheus-grafana-installation-on-k8s)
## Configuration Steps
#### Jenkins Configuration
- [Jenkins Login](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#lets-jenkins-configuration)
- [Jenkins plugins installations](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#jenkins-plugins-installations)
- [Tools Configuration](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#jenkins-tools-installations)
#### SonarQube Configuration
- [Create Token](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#create-sonarqube-token)
- [Create Webhook](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#create-sonarqube-webhook)
- [Jenkins Sonaqube Integration](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#jenkins-sonaqube-integration)
- [Sonarqube ReactJS Project](https://github.com/jeetu844/Shopping-reactJS-DevOps?tab=readme-ov-file#reactjs-sonaqube-project)

## Jenkins Installation

Jenkins Installation on CentOS 7

```bash
yum update -y
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum -y upgrade
sudo yum -y install java-11-openjdk
sudo yum -y install jenkins
sudo systemctl daemon-reload
service jenkins start
systemctl enable jenkins

```

## Docker Installation
Docker Installation on CentOS 7

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2 vim git
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce docker-ce-cli containerd.io
systemctl enable docker
systemctl start docker
usermod -aG docker $USER
newgrp docker
chmod 777 /var/run/docker.sock

```

## SonarQube Installation
SonarQube Installation using Docker Image

```bash
docker run -d -p 9000:9000 sonarqube:lts

```

## Trivy Installation 
Trivy Installation on CentOS 7

```bash
echo "[trivy]
name=Trivy repository
baseurl=https://aquasecurity.github.io/trivy-repo/rpm/releases/\$releasever/\$basearch/
gpgcheck=0
enabled=1" > /etc/yum.repos.d/trivy.repo

yum -y update
yum -y install trivy

```

## K8s Installation
K8s Installation on CentOS 7 **(For Master & Node)**

```bash
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubelet kubeadm kubectl
systemctl enable kubelet
systemctl start kubelet

sed -i '/swap/d' /etc/fstab
swapoff -a

cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system

rm -rf /etc/containerd/config.toml
systemctl restart containerd

```
**For K8s Master Only**
```bash
kubeadm init --pod-network-cidr=10.244.0.0/16

mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

export KUBECONFIG=/etc/kubernetes/admin.conf

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

```
**For K8s Node Only**
- Replace this token with original which you get on master Node
```bash
kubeadm join 192.168.63.76:6443 --token u09eln.emqgd14u5p4wh2w0 \
--discovery-token-ca-cert-hash \
sha256:e5d568e17f8eda67c61b3f2addfcb74edc498a3b880c964b9a8072718a4e18ff

```
## Argocd Installation
Argocd Installation on K8s
```bash
yum -y install epel-release
yum -y install jq
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
kubectl patch service argocd-server -n argocd --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value": 30000}, {"op": "replace", "path": "/spec/type", "value": "NodePort"}]'
export ARGOCD_SERVER=`kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.nodePort.ingress[0].hostname'`
export ARGO_PWD=`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

```
***Paste URL in Web Browser***

`https://142.44.249.127:30000` // Replace ip with your 2nd Server ip

`Username = admin`
- For get Argocd password 
```bash
echo $ARGO_PWD

```

## Argocd Configration
We will import k8s manifest yaml file from git repository (github) so we will use some steps
- Click on settings logo on Argocd leftbar
![settings logo](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-1.png)
- Click on Repositories
![Repositories](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-2.png)
- We will connect with github using https protocol so click on click on HTTPS
![HTTPS](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-3.png)
- We will copy repository https url from github.
![repository https url](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-4.png)
- We will paste this url on Argocd , If your repository is private then you have to fill username/password fields also.
- After then click on Connect Button
![Connect](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-5.png)
- You will get connection successful status.
- After that click on Apps button which is below of argocd logo in leftbar
![Apps button](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-6.png)
- We will create new App so click on new App
![new App](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-7.png)
- We will fill general informations -
  - Application Name: shopping
  - Project Name: default
  - Repository URL: Select your github url
  - Revision: HEAD
  - Path: ./ (It means your k8s yaml are in rool directory in your repo)
  - Clister URL: Select "Kubernetes.default.svc"
  - Namespace: default (Where you want to run your pods)
- Click on Create Button
![general informations1](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-8.png)
![general informations2](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-9.png)
- Your App have been Successfully created , And your pod has Successfully created on default Namespace.
- Click on Shopping for Detail view.
![Successfully created](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-10.png)
- You can see your all services and pods.
![services and pods](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-11.png)

***If you want anything to be updated in GitHub's yaml file, your Kubernetes cluster should also be updated at the same time.
For that we have to use webhook in GitHub***
- Click on Settings of your github repository.
![Settings](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-12.png)
- Click on Webhooks on Github's leftbar.
![Webhook1](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-13.png)
- Click on Add webhook.
![Webkool2](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-14.png)
- Fill all Details -
  - Payload Url: Your Argocd URL (2nd Server IP) :30000/api/webhook
  - Content type: application/json
  - Secret: you can put anything or leave Blank
  - SSL Verification: Disable
- Add webhook
![Add webhook](https://raw.githubusercontent.com/jeetu844/screenShots/main/Argocd/step-15.png)
Now k8s update automatically when repository will update.
## Prometheus Grafana Installation on K8s
### Helm Installation
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

```
- See the Helm version
```bashh
helm version --client

```
```bash
helm repo add stable https://charts.helm.sh/stable
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

```
```bash
kubectl create namespace prometheus
helm install stable prometheus-community/kube-prometheus-stack -n prometheus

```

To make Prometheus and grafana available outside the cluster, use LoadBalancer or NodePort instead of ClusterIP.
```bash
kubectl patch service stable-kube-prometheus-sta-prometheus -n prometheus --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value": 30002}, {"op": "replace", "path": "/spec/type", "value": "NodePort"}]'
kubectl patch service stable-grafana -n prometheus --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value": 30001},{"op": "replace", "path": "/spec/type", "value": "NodePort"}]'

```

```bash
kubectl get svc -n prometheus

```
***Paste URL in Web Browser***

`http://142.44.249.127:30001` Replace ip with your 2nd Server ip

`UserName: admin` `Password: prom-operator`

## Grafana Configration
We are using Prometheus for kubernetes Cluster Data Source which will provide data to Grafana And We are using Grafana for kubernetes Cluster visualization.
We are using some steps for K8s Cluster Dashboards.

- Import Dashboard
  - Click on Top Right + button then click import dashboard
![import dashboard](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-1.png)
  - Enter 15661 and click on Load for load predefined Dashboard.
![predefined Dashboard](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-2.png)
- Select Prometheus Data Source and Click on Import
![Prometheus Data Source](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-3.png)
- K8s Cluster Dashboard has successfully Loded.
![K8s Cluster Dashboard](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-4.png)
- We import new Dashboard with same process with id no 3119
![new Dashboard](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-5.png)
- K8s Cluster Another Dashboard has successfully Loded.
![Dashboard2](https://raw.githubusercontent.com/jeetu844/screenShots/main/grafana/step-6.png)
### Let's Jenkins Configure
***Paste URL in Web Browser***

`http://142.44.249.117:8080` Replace ip with your 1st Server ip

![Jenkins login password](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/LoginPage.jpeg)

For get Administrator password
```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```
#### Now, install the suggested plugins.
![Suggested Plugins](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/suggested-plugins.png)
#### Jenkins plugins installations
Goto Manage Jenkins -> Plugins -> Available plugins
- Eclipse Temurin Installer (Install without restart)
- SonarQube Scanner (Install without restart)
- NodeJs Plugin (Install Without restart)
- OWASP Dependency-Check (Install Without restart)
- Docker (Install Without restart)
- Docker Commons (Install Without restart)
- Docker Pipeline (Install Without restart)
- Docker API (Install Without restart)
- Docker-build-step (Install Without restart)
- Slack (Install Without restart)
- Splunk (Install Without restart)

![Jenkins JDK Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/JDK-plugin.jpeg)

![Jenkins NodeJS Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/NodeJS-plugin.jpeg)

![OWASP Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/owasp.jpeg)

![Docker Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/docker.jpeg)

![slack](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/slack-plugin.png)

**Restart your Jenkins** `http://142.44.249.117:8080/restart` **Replace ip with your 1st server ip**

#### Jenkins Tools installations
Goto Manage Jenkins -> Tools
- ADD JDK
  - Name -> "jdk17"
  - Click on "Install automatically"
  - Add Installer "Install from adoptium.net"
  - Version "jdk-17.0.8.1+1"
![Jenkins JDK Configuration](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Jenkins-JDK-Configure.png)
- SonarQube Scanner installations
  - Click on "Add SonarQube Scanner"
  - Name "sonar-scanner"
![Jenkins Sonar Scanner Tool](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Jenkins-SonarTool-installation.png)
- NodeJS installations
  - Click on "Add NodeJS"
  - Name "nodejs16"
  - Click on "Install automatically"
  - Select "NodeJS 16.2.0"
![Jenkins NodeJS Tool](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Jenkins-NodeJS-Tool.png)
- Dependency-Check installations
  - Click on "Add Dependency-Check"
  - Name "DP-Check"
  - Click on "Install automatically"
  - Click on "Add Installer"
  - Select "Install from github.com"
  - Select Version "dependency-check 6.5.1"
![Jenkins DP-Check Tool](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Jenkins-DP-Check-Tool.png)
- Docker installations
  - Click on "Add Docker"
  - Name "docker"
  - Click on "Install automatically"
  - Click on "Add Installer"
  - Select "Download from docker.com"
  - Docker version "latest"
![Jenkins Docker Tool](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Jenkins-Docker-Tool.png)

### Let's SonarQube Configure

`http://142.44.249.117:9000` **Replace ip with your 1st Server ip**

- Default Credentials
  - Username : admin
  - Password : admin
#### Create SonarQube Token
- Click on "Administration"
![SonarQube Step-1](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/token-step-1.png)
- Click on "Security"
- Click on "Users"
![SonarQube Step-2](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/token-step-2.png)
- Click on Update Tokens
![SonarQube Step-3](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/token-step-3.png)
- Enter Token Name "Jenkins"
- Click on "Generate"
- Copy your Token
![SonarQube Step-4](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/token-step-4.png)
#### Create SonarQube Webhook
- Click on "Administration"
![SonarQube Webhook Step-1](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/token-step-1.png)
- Click on "Configuration"
- Click on "Webhooks"
![SonarQube Webhook Step-2](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/webhook-step-1.png)
- Click on "Create"
![SonarQube Webhook Step-3](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/webhook-step-2.png)
  - Name "Jenkins"
  - URL `http://142.44.249.117:8080/sonarqube-webhook/` **Replace ip with your 1st Server**
  - Click on "Create"
![SonarQube Webhook Step-4](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/webhook-step-3.png)

### Jenkins Sonaqube Integration
- Open Jenkins
- Goto Manage Jenkins -> Credentials
- Click on "(global)"
![jenkins-int-step-1](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/jenkins-int-step-1.png)
- Click on "Add Credentials"
![jenkins-int-step-2](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/jenkins-int-step-2.png)
  - Select in Kind "Secret text"
  - Paste sonarqube token in "Secret"
  - ID "sonar-token"
  - Description "sonar-token"
![jenkins-int-step-3](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/jenkins-int-step-3.png)
- Goto Manage Jenkins -> System -> SonarQube servers
- Click on "Add SonarQube"
  - Name "sonar-server"
  - Server URL `http://142.44.249.117:9000` **Replace ip with your 1st Server**
  - Server authentication token "sonar-token"
- Apply & Save
![jenkins-int-step-4](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/jenkins-int-step-4.png)

### ReactJS Sonaqube Project
- Goto Sonarqube Projects
![project-step-1](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-1.png)
- Click on "Manually"
![project-step-2](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-2.png)
  - Project display name "shopping"
  - Project key "shopping"
  - Main branch name "main"
  - Clink on "Set Up"
![project-step-3](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-3.png)
- Click on "Locally"
![project-step-4](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-4.png)
- Select "Use existing token"
  - Paste SonarToken
  - Click on "Continue"
![project-step-5](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-5.png)
- Click on "Other (for JS, TS, Go, Python, PHP, ...)"
  - Click on "Linux"
![project-step-6](https://raw.githubusercontent.com/jeetu844/screenShots/main/SonarQube/project-step-6.png)

## DockerHub Configration
- Create account on [dockerhub](https://hub.docker.com)
- Create a new repository on dockerhub
![DockerHub](https://raw.githubusercontent.com/jeetu844/screenShots/main/dockerHub/step-1.png)
![repository](https://raw.githubusercontent.com/jeetu844/screenShots/main/dockerHub/step-2.png)

**DockerHub Integration with Jenkins**
- Open Jenkins -> Goto Manage jenkins -> Credentials
- Click on (global)
![global](https://raw.githubusercontent.com/jeetu844/screenShots/main/dockerHub/step-3.png)
- Click on Add Credentials
![Credentials](https://raw.githubusercontent.com/jeetu844/screenShots/main/dockerHub/step-4.png)
- Select in Kind: Ussername with password
  - Usernam: Your DockerHub account Ussername
  - Password: Your DockerHub account Password
  - ID: dockerhub **(This name we will use in Jenkins Pipeline)**
  - Description: dockerhub
![dockerhub](https://raw.githubusercontent.com/jeetu844/screenShots/main/dockerHub/step-5.png)
## Github Configration
**We will create Github Token**
- Click on github logo to top right side
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-1.png)
- You will see a dropdown menu, Click on Settings
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-2.png)
- Click on Developer Settings on sidebar
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-3.png)
- Click on "Personal access tokens"
  - Click on "Tokens (Classic)"
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-5.png)
- Click on "Generate new token"
  - Click on "Generate new token (Classic)"
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-6.png)
- Enter a name in Notes (Any name which you want)
  - Expiration: You can set days OR set "No Expiration"
  - Select All checkbox 
  - Generate token
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-7.png)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-8.png)
**Copy this token and save it**
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-9.png)
### Create new Repository for K8s Manifest
- Click on + button then click on "New Repository"
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-10.png)
- Repository Name: "shopping-manifest" **We will use this name in jenkins pipeline**
- Select Public (For Private please check my another project)
- Create Repository
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-11.png)

### Add github token in Jenkins
- Goto Manage Jenkins -> Credential in Jenkins
- Click on "(global)"
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-12.png)
- Click on Add Credentials
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-13.png)
- Kind: Select "Secret text"
- Secret: Paste you github token
- ID: github-token (this name will use in jenkins pipeline)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gitHub/step-14.png)
## Gmail Jenkins Integration
We will send email of Jenkins pipeline's Result, So we are using Gmail SMTP
We have to create gmail app password 1st, using some steps we will create gmail app password.
- Click on top right side image in gmail account then click on "Manage your google account"
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-1.jpg)
- Search `app passwords` in search baar then click on `App Passwords`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-2.png)
- Provide App name (In example we are providing `Jenkins`) and then click on `Create` Button.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-3.png)
- You will get a password , **Copy this password.**
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-4.jpg)
- Open your Jenkins , Goto `Manage Jenkins` -> `Credentials` than click on `(global)`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-5.png)
- Click on `Add Credentials`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-6.png)
- Kind: `Username with password`
- Username: `your gmail address`
- Password: `your app password`
- ID: `gmail`
- Click on `Create`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-7.png)
- Goto `Manage Jenkins` -> `System` And Update System Admin e-mail address and name.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-8.png)
- On same page scroll down and goto on `Extended E-mail Notification`
  - SMTP server: ` smtp.gmail.com `
  - SMTP port: ```465```
  - Credentials: `Select gmail`
  - Mark on `Use SSL`
  - Default Content type: `HTML`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-9.jpg)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-10.png)

**Now goto on `E-mail Notification`**
- Full all fields like previously And click on `Test configration`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-11.png)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-12.png)
- You will receive test email from Jenkins
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/gmail/step-13.png)


## Slack Jenkins Integration
- Create account on [Slack](https://slack.com/)
- Enter your workspace name For example `Jenkins`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-1.png)
- Enter your Name and Click on `Next`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-2.png)
- You can `Skip` step 3
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-3.png)
- Enter your project name For example `myproject`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-4.png)
-  Click on `Jenkins` then click on `Settings & administration` then click on `Manage apps`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-5.png)
- Search `jenkins ci` in search bar then click on `Jenkins CI`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-6.png)
- Click on `Add to slack`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-7.png)
- In `Post to Channel` section select `#myproject` (In our case `#myproject` is our channel name)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-8.png)
- Now click on `Add Jenkins CI Integration`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-9.png)
- Copy `Team Subdomain` Value and Token , It will use in jenkins
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-10.png)
- Open Jenkins and goto `Manage Jenkins` -> `Credentials` then click on `(global)`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-11.png)
- Click on `Add Credentials`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-12.png)
- Select `Secret text` in kind section
- You have to paste your slack token in `Secret`
- ID: `slack`
- Click on `Create`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-13.png)
- Now goto `Manage Jenkins` then click on `System` then goto `Slack` section
  - Paste your workspace name which you copied from slack
  - Select `sclack` in Credential
  - Enter your channel name (in our case our channel name is `#myproject`)
  - Click on `Test Connection` you will receive `Seccess` message
- Then Click on `Apply` And `Save` Button
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-14.png)
- In Slack dashboard you will receive a message from Jenkins
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/slack/step-15.png)

## Splunk Jenkins Integration
- Create Account on [Splunk](https://www.splunk.com)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-1.png)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-2.png)
- After Create Account click on `My Dashboard`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-3.png)
- Click on `Products` then click on `Free Trials & Downloads`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-4.png)
- Click on `Get My Free Trial`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-5.png)
- Click on `Linux` then click on `Download Now`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-6.png)
- Stop Downloading and click on `Command Line (wget)` then you will get wget Command and copy this
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-7.png)
- Paste this comman on your server 
```
wget -O splunk-9.1.2-b6b9c8185839.x86_64.rpm "https://download.splunk.com/products/splunk/releases/9.1.2/linux/splunk-9.1.2-b6b9c8185839.x86_64.rpm"
sudo rpm -i splunk-9.1.2-b6b9c8185839.x86_64.rpm
sudo /opt/splunk/bin/splunk start
```
- After this you will get `Splunk general terms` Keep pressing the Enter button until the Terms page is completed.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-8.png)
- Set username and password for for access your splunk UI
- Now open your web browser and paste your splunk server ip with port 8000 in my case my ip is `142.44.249.127` so my splunk url will be `http://142.44.249.127:8000`
- Enter your username/password which you have created on cli 
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-9.png)
- Now click on `Apps` then click on `Find More Apps`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-10.png)
- Search `jenkins` in search bar and click on `Install`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-11.png)
- Now you will have to enter the username and password of your Splunk.com account.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-12.png)
- Click on `Done`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-13.png)
- Now Click on `Settings` then click on `Data inputs`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-14.png)
- Click on `HTTP Event Collector`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-15.png)
- Click on `Global Settings`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-16.png)
- Click on `Enabled` then uncheck `Enable SSL` then click on `Save`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-17.png)
- Now Click on `New Token`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-18.png)
- Enter token name (Anything which you want) in mycase i entered `Jenkins` then click on `Next` button 
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-19.png)
- Now click on `Review`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-20.png)
- Click on `Submit >` Button 
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-21.png)
- You have get token copy this and click on `Start Searching`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-22.png)
- Copy your splunk token
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-23.png)
-  Open your Jenkins then click on `Manage Jenkins`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-24.png)
- Click on `System`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-25.png)
- Goto on `Splunk for Jenkins Configration` Section
- Check on `Enable`
- `HTTP input Host`: **Your Splunk Server IP**
- `HTTP Input toke`: **Your Splunk Token**
- `Jenkins Master Hostname`: `Your Jenkins server IP`
- Click on `Test Connection` you will get `Splunk connection verified` message
- Now click on `Apply` then click on `Save` button
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-26.png)
- Now you have to restart your Jenkins server 
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-27.png)
- For Splunk server restart click on `Settings` then click on `Server Controls`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-28.png)
- Now click on `Rstart Splunk`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/splunk/step-29.png)

## Jenkins Pipeline
We have integrated all tool with Jenkins, So now we start jenkins pipeline.
- Click on `New Item`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-1.png)
- Enter your project name , in mycase i entered `Shopping`
- Click on `Pipeline`
- Click on `OK`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-2.png)
- Check-mark on `Discard old buids`
- Enter any number in `Max # of builds to keep`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-3.png)
- Now checkmark on `This project is parameterized` then select `String Parameter`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-4.png)
- Now we will set Patameters - 
  - Name: `GithubUser` (This is key , Don't change it)
    - Default Value: `jeetu844` (This is my github username, Please enter your github username)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-5.png)
  - Name: `GithubRepo` (This is key , Don't change it)
    - Default Value: `Shopping-reactJS-manifest` (Please enter your Repository name which you have created for manifest)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-6.png)
  - Name: `DockerUser` (This is key , Don't change it)
    - Default Value: `jeetu844` (Please enter your Dockerhub username)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-7.png)
  - Name: `DockerRepo` (This is key , Don't change it)
    - Default Value: `shopping` (Please enter your Dockerhub repository name)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-8.png)
  - Name: `MyName` (This is key , Don't change it)
    - Default Value: `Jitendra Sharma` (Please Enter your Name)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-9.png)
  - Name: `MyEmail` (This is key , Don't change it)
    - Default Value: `jeetu.844@gmail.com` (Please Enter your Email Address)
    - Check mark `Trim the string`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-10.png)
- In Pipeline section select `Pipeline script from SCM`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-11.png)
- Copy this: 
```bash
https://github.com/jeetu844/Shopping-reactJS-DevOps.git
```
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-12.png)
- Select `Git` in SCM
- Paste this in `Repository URL`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-13.png)
- Enter `./main` in Branch Specifier
- Click on Save button
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-14.png)
- Click on `Build with Parameters`
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-15.png)
- Click on `Build` button
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-16.png)
- Process will be start , it will take some time , Ater complete all stages you can its all details.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-17.png)
- You can check all stages full Process.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-18.png)
- You can check code quality in sonarqube.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-19.png)
- You can check Dependencies Result
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-20.png)
- In your dockerhub account docker images has uploded
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-21.png)
- In github manifest repository k8s yml file has updated.
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-22.png)
- Click on deployment.yml and ckeck image version has changed according to jenkins build number
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-23.png)
- Click on splunk for check jenkins metrics dashboard
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-24.png)
![](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/Shopping-reactJS-DevOps/step-25.png)
## Authors

- [@Jitendra Sharma](https://www.linkedin.com/in/jitendra-kumar-sharma-0b662751/)
  