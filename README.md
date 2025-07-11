# Jenkins-Java-web-app
End-to-end Jenkins pipeline will automate the entire CI/CD process for a Java application, from code checkout to production deployment, using popular tools like SonarQube, Argo CD, Helm, and Kubernetes.

## Prerequisites: ##

1. Source code hosted on Git repository
2. Jenkins server
3. Kubernetes cluster
4. Helm package manager
5. Argo CD

## Steps ##

### 1. Create an ec2 Instance ###
- Got to the AWS console > EC2 Dashboard > Create Instance > Launch Instance. 
- For the Purpose of the demo, I have used t2.large as type to handle heavy workloads such as Jenkins server, Docker Agent, Maven Integration, Sonar qube, etc.
  <img width="1988" height="1194" alt="image" src="https://github.com/user-attachments/assets/dfa2b1bc-1a0f-4ae6-a85c-2155daaa2b71" />

### 2. Installing Jenkins on the Ec2 Instance 
- Run the following commands
```
sudo apt update 
sudo apt install openjdk-17-jre

```
- verify the Java is installed
```
java -version
```
- Installing Jenkins
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
- By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.
 Go to EC2 Dashboard > Instances >  Click on Security groups > click on inbound rules > edit inbound rules - in my case, I allowed All traffic).
<img width="2374" height="396" alt="image" src="https://github.com/user-attachments/assets/933f5cee-ca28-41d4-9813-d0a4ae9b90e1" />

### 3. Logging into Jenkins server

- http://PublicIpAddressofec2instance:8080 (you can get the public IP address of the ec2 instance from the AWS console page)
- After you login to Jenkins, - Run the command on your laptop terminal to copy the Jenkins Admin Password - sudo cat /var/lib/jenkins/secrets/initialAdminPassword - Enter the Administrator password
<img width="2582" height="1192" alt="image" src="https://github.com/user-attachments/assets/0d08d0b9-1c91-4242-9b17-81a4f9e65425" />
- Click on Install Suggested Plugins
  <img width="2582" height="1192" alt="image" src="https://github.com/user-attachments/assets/00a34a35-5381-4f6f-82c2-f337cba69091" />
- Wait for the Jenkins to Install suggested plugins
  <img width="2582" height="1192" alt="image" src="https://github.com/user-attachments/assets/a9b2a8b2-191b-41c9-9495-a9c970be5081" />
- Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]
  <img width="1980" height="1232" alt="image" src="https://github.com/user-attachments/assets/f14f7af7-704a-4f18-bd69-b149bc921b0c" />
  Jenkins Installation is Successful. You can now starting using the Jenkin
  <img width="1980" height="1232" alt="image" src="https://github.com/user-attachments/assets/fd56c189-8fc6-43f2-9d01-ea47ae188207" />
  
### 4. Importing Jenkins file 
referenced my github url link and the path to my JenkinsFile as well. I have written a declrative Jenkins pipeline. 

### 5. Installing Docker pipeline plugin
Go to Manage Jenkins > Plugins > available plugins 
search for 'Docker Pipeline' and click on Install without restart 

### 6. Installing Sonar plugin
Go to Manage Jenkins > Plugins > available plugins 
search for Sonar, and select 'Sonarqube Scanner' and click on Install without restart 

### 7. Pre-requisites for Sonar qube 
System Requirements: 
Java 17+ (Oracle JDK, OpenJDK, or AdoptOpenJDK)
Hardware Recommendations:
   Minimum 2 GB RAM
   2 CPU cores

### 8. Configure sonar qube locally

- Update the packages and install unzip

```
sudo apt update && sudo apt install unzip -y
```

- adding user sonar qube automatically creates the home directory, sets permissions, and prompts for things like a password and full name.

```
sudo adduser sonarqube
```

- Download the binaries using wget
```
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
```
- now unzip the downloaded sonarqube binary
```
unzip *
```

- Assign permssions
``` 
chown -R sonarqube:sonarqube /opt/sonarqube
chmod -R 775 /opt/sonarqube
```

- start sonar qube
```
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
```

by default, Soanr qube runs on port 9000. Open the browser and paste the PublicIp address of the ec2 instance along with the port number. Hit enter.

- Logging into Sonarqube : Enter the User name & password 

- updating the password: User name & password - admin (for demo purposes)

- To integrate Sonarqube with Jenkins, we need to generate sonarqube token : Go to the administrator > my account

- Generating token: Enter a token name and click generate. Copy it. 

- Add the token to the Jenkins: Go to Jenkins > Manage Jenkins > Manage credentials > system > Global credentials > add credentials > add the sonarqube token ( we will use this token in the Jenkinsfile)

### 9. Installing Docker on the EC2 instance. 

- On the EC2 instance, switch to root user and execute the following command. 

```
sudo apt update
sudo apt install docker.io
```
- Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```
- Restart the Jenkins. (It's always a good practice to restart the Jenkins after installing plugins or whenever making config changes). After restarting, Jenkins will ask for user name and password, enter it and select the 'keep me signed in' option. 

- The Docker agent configuration has been set successfully.

### 10. Adding the pom.xml file (Project Object Model file)

- on your git repo > Go to the java-maven-sonar-argocd-helm-k8s/spring-boot-app
- create a file pom.xml and write the XML file. this will tell the MAven target how to build the file, downloading the dependencies, plugins and also explains about the project structure and metadata. 

### 11. Create a deployment yaml file for the spring boot application on the same git repository 

- Create a folder named spring-boot-app-manisfests > deployment.yaml 
- within this yaml file, mention the <replaceImageTag>, so that during the update and deploy stage, the shell script will take the image tag as per the Docker registry and update it to the yaml file.

### 12. Adding Docker Registry credentials and Git token to the Jenkins. 

Docker Registry:

- during the Build and push Docker image stage, the Jenkins needs the Docker Registry credentials in order to push the image.
- Go to Jenkins > manage jenkins > manage credentials > stores scoped to jenkins > system > global credentials > add new credentials

- New credentials : kind as 'username and password' > scope 'global' > username 'mathangi03' > password '********' > id as 'docker-cred'. Click create.
- Docker credentials are stored on to the Jenkins credentials store.

Git : 

- Go to github > settings > developer settings > personal access tokens > token classic . It might prompt you enter the password.
- New personal access token : Note 'Jenkins' > Expiration ' 7 days' > select scopes for the personal tokens > generate token. Copy the token. 

- During the Update and deploy stage, the Git credentials and git token are needed for the shell script to update the manifests file which is on the git.
- Go to Jenkins > manage jenkins > manage credentials > stores scoped to jenkins > system > global credentials > add new credentials

- New credentials : Kind as 'secret text' > scope 'global' > secret ' paste the github token' > id ' > github' > click create.
- Github token is stored on to the Jenkins credentials store.

Restart the Jenkins !!!!


### 13. Installating MiniKube on the laptop (Kubernetes deployment)

Minikube - A Local kubernetes. A Docker or a virtual machine environment is needed for starting the local kubernetes. 

Pre-requisites : 
1. 2 CPUs or more
2. 2 GB of Free memory
3. 20 GB of free disk space
4. Internet connection
5. Container or virtual machine manager (such as Dcoker, Hyperkit, Hyper-V, podman, Virtualbox, VMware Fusion/Workstation)

Based on the target platform and architecture, select the options on the minikube page and get the installation command. 

- from the laptop terminal with admin access(not as root user), run : ``` minikube start ``` and for the driver mention :-- memory=4098 --driver=hyperkit

```
minikube start --memory=4098 --driver=hyperkit
```

This will install the Kubernetes cluster. (For the first time, it might take some time) 

### 14. Insallation of Operator 

Why operator - So, whenever you are Installing any kubernetes controller, you should always go with the operator approach. Operators will manage the lifecycle of the kubernetes controllers. In future, if there are any updates to the controller, or version changes, telemetry related info - it will be taken care by operators. 

- Go to the operatorhub.io > serach for argocd > click install - https://operatorhub.io/operator/argocd-operator. Follow the steps

  1. Installation of OLM (operator Lifecyle manager)
     
  ```
  curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.32.0/install.sh | bash -s v0.32.0
  ```
  2. Installing the operator
 
  ```
  kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
  ```
  This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.

  3. After install, watch your operator come up using next command.
 
  ```
  kubectl get csv -n operators
  ```

### 15. Installation of Argo cd Controller 

- Go to argo cd operator docs > basics > copy the Argo cd cluter with default configuration.
- go to your laptop terminal > create vim argocd-basic.yml
- paste the config and save it
- now,
  ```
  Kubectl apply -f argocd-basic.yml
  ```
- Argocd controller will be up and running in some time.

### 16. Configuring the Application details on to the Argocd UI 

- On your laptop enter the service command
  ```
  Kubectl get svc
  ```
- Now, edit the ``` Kubectl edit svc example-argocd-server```. This is for changing the type from Cluster IP to Nodeport to access the Argocd server on the browser.
- Once again run ``` Kubectl edit svc example-argocd-server``` and you can verify the type for the argocd server should be changed to nodeport.
- To get the url on a minikube, Minikube itself gives a service url.
```
Minikube service list
```
- From the list, click on the url for the argocd server. 

- you will be redirected to the argocd server and you will be prompted to enter username and password.
- username 'admin' and password has to be copied from the secrets which is in the kubernetes cluster. Go to the terminal and tyep ``` kubectl get secret ```, refer the name - which is 'example-argocd-cluster' and type ``` Kubectl edit secret example-argocd-cluster ```
- copy the password. this password is base64 encrypted. 
- To decrypt this, type ``` echo 'copied encrypted password'= | base64 -d ```
- Now you will get the decrypted password. Copy it and paste on the argocd ui. The Login will be successful

  Applying Application details :
  - click create application
  - App name 'test' > project name 'default' > sync policy ' automatic' > under source, mention the repo url > branch as 'head' > mention the path to the manifest file on your git > under destination, provide the kubernetes cluster url > provide namespace of the argocd controller > click create
  - Argocd will try to fetch the information and deploy the app on kubernetes cluster. 

  



















   





  





