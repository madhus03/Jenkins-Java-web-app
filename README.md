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
<img width="800" height="402" alt="image" src="https://github.com/user-attachments/assets/3195a96b-ce04-4b5e-a939-a71f8e8cbce5" />

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

- Logging into Sonarqube
Enter the User name & password 

- updating the password
User name & password - admin (for demo purposes)

- To integrate Sonarqube with Jenkins, we need to generate sonarqube token
Go to the administrator > my account

- Generating token
Enter a token name and click generate

- Add the token to the Jenkins
Go to Jenkins > Manage Jenkins > Manage credentials

















   





  





