
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
[Click Here]() For Argocd configration
## Prometheus Grafana Installation on K8s
Prometheus Grafana Installation on K8s using helm
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

![Jenkins JDK Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/JDK-plugin.jpeg)

![Jenkins NodeJS Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/NodeJS-plugin.jpeg)

![OWASP Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/owasp.jpeg)

![Docker Plugin Installation](https://raw.githubusercontent.com/jeetu844/screenShots/main/Jenkins/docker.jpeg)

**Restart your Jenkins** ```<Your PublicIP:8080/restart>```

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
```
<Your Public IP Address:9000>
```
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
  - URL "<Jenkins PublicIP:8080/sonarqube-webhook/>
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
  - Server URL "<http://Sonar Server PublicIP:9000>"
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

## Authors

- [@Jitendra Sharma](https://www.linkedin.com/in/jitendra-kumar-sharma-0b662751/)
  