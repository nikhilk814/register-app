# Install and Configure Jenkins - Master and Agent

This guide will walk you through the installation and configuration of Jenkins on your server, along with setting up Java and SSH for Jenkins.

## Install Java

1. Update the package list and upgrade existing packages:

    ```bash
    $ sudo apt update
    $ sudo apt upgrade
    ```

2. Set the hostname (optional, but useful for identifying your server):

    ```bash
    $ sudo nano /etc/hostname
    $ sudo init 6   # Reboot the server for the hostname change to take effect
    ```

3. Install OpenJDK 17 (or the desired Java version):

    ```bash
    $ sudo apt install openjdk-17-jre
    ```

4. Verify the Java installation:

    ```bash
    $ java -version
    ```

## Install Jenkins

Follow these steps to install Jenkins on your server:

1. Add the Jenkins repository key:

    ```bash
    $ curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    ```

2. Add the Jenkins repository to the package sources:

    ```bash
    $ echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    ```

3. Update the package list:

    ```bash
    $ sudo apt-get update
    ```

4. Install Jenkins:

    ```bash
    $ sudo apt-get install jenkins
    ```

5. Enable Jenkins to start at boot:

    ```bash
    $ sudo systemctl enable jenkins
    ```

6. Start Jenkins as a service:

    ```bash
    $ sudo systemctl start jenkins
    ```

7. Check Jenkins service status:

    ```bash
    $ systemctl status jenkins
    ```

## Configure SSH

1. Edit the SSH server configuration file:

    ```bash
    $ sudo nano /etc/ssh/sshd_config
    ```

2. Reload the SSH server configuration:

    ```bash
    $ sudo service sshd reload
    ```

3. Generate SSH keys (Choose either RSA or Ed25519):

    For RSA keys:

    ```bash
    $ ssh-keygen
    ```

    OR for Ed25519 keys:

    ```bash
    $ ssh-keygen -t ed25519
    ```

4. Navigate to the SSH key directory (usually `~/.ssh`) and make any necessary modifications:

    ```bash
    $ cd ~/.ssh
    ```

That's it! You've successfully installed and configured Jenkins on your server, along with Java and SSH for Jenkins to use.


# Setting Up SonarQube on Ubuntu

This guide will walk you through the process of setting up SonarQube on an Ubuntu-based system. Please follow the steps below to get SonarQube up and running.

## Update Package Repository and Upgrade Packages

   ```shell
   $ sudo apt update
   $ sudo apt upgrade
   ```
## Add PostgreSQL Repository
   ```bash
   $ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
   $ wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
   ```
## Install PostgreSQL
   $ sudo apt update
   $ sudo apt-get -y install postgresql postgresql-contrib
   $ sudo systemctl enable postgresql
## Create Database for Sonarqube
    $ sudo passwd postgres
    $ su - postgres
    $ createuser sonar
    $ psql
    $ ALTER USER sonar WITH ENCRYPTED password 'sonar';
    $ CREATE DATABASE sonarqube OWNER sonar;
    $ grant all privileges on DATABASE sonarqube to sonar;
    $ \q
    $ exit
# Add Adoptium repository
    $ sudo bash
    $ wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
    $ echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
 # Install Java 17
    $ apt update
    $ apt install temurin-17-jdk
    $ update-alternatives --config java
    $ /usr/bin/java --version
    $ exit 
# Linux Kernel Tuning
   ## Increase Limits
    $ sudo vim /etc/security/limits.conf
   
   ## Paste the below values at the bottom of the file
    sonarqube   -   nofile   65536
    sonarqube   -   nproc    4096
   
   ## Increase Mapped Memory Regions
     $ sudo vim /etc/sysctl.conf
     
  ## Paste the below values at the bottom of the file
    vm.max_map_count = 262144
   
# Sonarqube Installation #
## Download and Extract
    $ sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
    $ sudo apt install unzip
    $ sudo unzip sonarqube-9.9.0.65466.zip -d /opt
    $ sudo mv /opt/sonarqube-9.9.0.65466 /opt/sonarqube
## Create user and set permissions
     $ sudo groupadd sonar
     $ sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
     $ sudo chown sonar:sonar /opt/sonarqube -R
## Update Sonarqube properties with DB credentials
     $ sudo vim /opt/sonarqube/conf/sonar.properties
     
## Find and replace the below values, you might need to add the sonar.jdbc.url
     sonar.jdbc.username=sonar
     sonar.jdbc.password=sonar
     sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
     
## Create service for Sonarqube
```bash
$ sudo vim /etc/systemd/system/sonar.service
```
## Paste the below into the file
     [Unit]
     Description=SonarQube service
     After=syslog.target network.target

     [Service]
     Type=forking

     ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
     ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

     User=sonar
     Group=sonar
     Restart=always

     LimitNOFILE=65536
     LimitNPROC=4096

     [Install]
     WantedBy=multi-user.target

## Start Sonarqube and Enable service
     $ sudo systemctl start sonar
     $ sudo systemctl enable sonar
     $ sudo systemctl status sonar

## Watch log files and monitor for startup
     $ sudo tail -f /opt/sonarqube/logs/sonar.log  
### default sonarqube port:9000

# Setup Bootstrap Server for eksctl and Setup Kubernetes using eksctl

## Install AWS CLI on the above EC2
Follow the steps below to install AWS CLI:

```bash
$ sudo su
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ apt install unzip
$ unzip awscliv2.zip
$ sudo ./aws/install
```
## OR
```
$ sudo yum remove -y aws-cli
$ pip3 install --user awscli
$ sudo ln -s $HOME/.local/bin/aws /usr/bin/aws
$ aws --version
```
## Installing kubectl
### Refer--https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html
```bash
$ sudo su
```
```bash
$ curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.1/2023-04-19/bin/linux/amd64/kubectl
```
```bash
$ ll , $ chmod +x ./kubectl  //Gave executable permisions
$ mv kubectl /bin   //Because all our executable files are in /bin
$ kubectl version --output=yaml
```
## Installing  eksctl
### Refer---https://github.com/eksctl-io/eksctl/blob/main/README.md#installation
```
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```
```bash
$ cd /tmp
$ ll
$ sudo mv /tmp/eksctl /bin
$ eksctl version
```
## Setup Kubernetes using eksctl
### Refer--https://github.com/aws-samples/eks-workshop/issues/734
```bash
$ eksctl create cluster --name virtualtechbox-cluster \
--region ap-south-1 \
--node-type t2.small \
--nodes 3 \
```
```
$ kubectl get nodes
```
# ArgoCD Installation on EKS Cluster 

### Step 1: First, create a namespace
    $ kubectl create namespace argocd

### Step 2: Next, let's apply the yaml configuration files for ArgoCd
    $ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

### Step 3: Now we can view the pods created in the ArgoCD namespace.
    $ kubectl get pods -n argocd

### Step 4: To interact with the API Server we need to deploy the CLI:
    $ curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
    $ chmod +x /usr/local/bin/argocd

### Step 5: Expose argocd-server
    $ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

### Step 6: Wait about 2 minutes for the LoadBalancer creation
    $ kubectl get svc -n argocd

### Step 7: Get pasword and decode it.
    $ kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
    $ echo WXVpLUg2LWxoWjRkSHFmSA== | base64 --decode

# Add EKS Cluster to ArgoCD
### Step 1: login to ArgoCD from CLI
    $ argocd login a2255bb2bb33f438d9addf8840d294c5-785887595.ap-south-1.elb.amazonaws.com --username admin

### Step 2:  cluster list
     $ argocd cluster list

### Step 3: Below command will show the EKS cluster
     $ kubectl config get-contexts

### Step 5: Add above EKS cluster to ArgoCD with below command
     $ argocd cluster add i-08b9d0ff0409f48e7@virtualtechbox-cluster.ap-south-1.eksctl.io --name virtualtechbox-eks-cluster

### Step 6: check services
     $ kubectl get svc

