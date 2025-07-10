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
<img width="1200" height="520" alt="image" src="https://github.com/user-attachments/assets/70f42125-42fc-4f17-adf9-4de3cf0b8b6b" />
<img width="1600" height="685" alt="image" src="https://github.com/user-attachments/assets/fb123f12-64c7-41bd-8a7b-31fb22e11f64" />
<img width="1200" height="587" alt="image" src="https://github.com/user-attachments/assets/0bfaba96-1935-4cb9-89c4-a325efe6e867" />
<img width="800" height="377" alt="image" src="https://github.com/user-attachments/assets/773d102f-62eb-4a19-a0f0-0c6b40cd6911" />

### 6. Installing Sonar plugin
Go to Manage Jenkins > Plugins > available plugins 
search for Sonar, and select 'Sonarqube Scanner' and click on Install without restart 
<img width="1600" height="806" alt="image" src="https://github.com/user-attachments/assets/f9e90ffd-ed94-4a7b-a5f5-be074d94529a" />

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
<img width="1800" height="100" alt="image" src="https://github.com/user-attachments/assets/59ed3112-eb1e-413b-9ba6-56a982e13408" />
<img width="800" height="176" alt="image" src="https://github.com/user-attachments/assets/250b2015-2e43-45cf-8605-5d1f3a310bcf" />
<img width="1200" height="527" alt="image" src="https://github.com/user-attachments/assets/b8436050-8118-4bf5-b458-eceb73106fdf" />

- Download the binaries using wget
```
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
```
<img width="1600" height="403" alt="image" src="https://github.com/user-attachments/assets/70e4ea6a-cf31-4e40-802a-efed52c6f552" />

- now unzip the downloaded sonarqube binary
```
unzip *
```
<img width="1800" height="125" alt="image" src="https://github.com/user-attachments/assets/3a6c6a7a-cd78-4e47-b2f0-b2d28a86f19f" />
<img width="400" height="203" alt="image" src="https://github.com/user-attachments/assets/7533cd7e-fa89-46fc-8522-a286d526a962" />
<img width="1600" height="122" alt="image" src="https://github.com/user-attachments/assets/ddfe6553-6513-4cdf-833e-ec7e216f96b3" />


- Assign permssions
``` 
chown -R sonarqube:sonarqube /opt/sonarqube
chmod -R 775 /opt/sonarqube
```

<img width="1200" height="113" alt="image" src="https://github.com/user-attachments/assets/ece3ecc0-7886-4eb7-b253-bd0a17f49d4c" />
<img width="1200" height="67" alt="image" src="https://github.com/user-attachments/assets/e116f052-5a1c-4fd8-b68e-8a2635a9a84b" />

- start sonar qube
```
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
```
<img width="1200" height="67" alt="image" src="https://github.com/user-attachments/assets/d4ce3600-e7ea-48ec-a01e-0b10d0dcbebc" />
<img width="1600" height="124" alt="image" src="https://github.com/user-attachments/assets/a606d5d0-67ee-4121-b95a-d58dfb55b14d" />
<img width="1200" height="130" alt="image" src="https://github.com/user-attachments/assets/19e3eb39-1e22-4cec-bbda-e82ab1ca207c" />

by default, Soanr qube runs on port 9000. Open the browser and paste the PublicIp address of the ec2 instance along with the port number. Hit enter. 
<img width="1800" height="134" alt="image" src="https://github.com/user-attachments/assets/eb074952-a77d-419e-9de2-402beea3631a" />

Sonar qube is starting..... 
<img width="400" height="183" alt="image" src="https://github.com/user-attachments/assets/8463a85b-9578-491f-8a81-979ca30cfd06" />
<img width="400" height="177" alt="image" src="https://github.com/user-attachments/assets/172a6647-a495-414e-b25a-3b8e7ee99a27" />

- Logging into Sonarqube
Enter the User name & password 
<img width="800" height="386" alt="image" src="https://github.com/user-attachments/assets/e7c1fd50-f1f8-4c88-af22-04f106c828e9" />

- updating the password
User name & password - admin (for demo purposes)
<img width="1200" height="442" alt="image" src="https://github.com/user-attachments/assets/e26f7abf-b467-4cc1-bb9f-2eeec2e38b5c" />

- Sonarqube console page
<img width="800" height="399" alt="image" src="https://github.com/user-attachments/assets/a3fc5a96-9b2e-45be-9029-ff5ef66283ef" />

- To integrate Sonarqube with Jenkins, we need to generate sonarqube token
Go to the administrator > my account
<img width="1200" height="597" alt="image" src="https://github.com/user-attachments/assets/4ae2a8c8-c5ef-42e4-abce-cf977540ef7f" />

- Generating token
Enter a token name and click generate
<img width="1600" height="708" alt="image" src="https://github.com/user-attachments/assets/23c6c7eb-08c9-4deb-81d8-9872308807a1" />
<img width="1600" height="378" alt="image" src="https://github.com/user-attachments/assets/ed2de331-259a-4de8-b86a-3d6c8b4754fe" />

- Add the token to the Jenkins
Go to Jenkins > Manage Jenkins > Manage credentials

<img width="1600" height="796" alt="image" src="https://github.com/user-attachments/assets/eb0ebc2c-fcf3-443d-b6e0-bc776aa6d813" />
<img width="1200" height="497" alt="image" src="https://github.com/user-attachments/assets/2dc670f5-a29d-4fb1-9aa9-f9e078fad866" />
<img width="1200" height="379" alt="image" src="https://github.com/user-attachments/assets/1b4332ea-66dc-4184-ac70-a0d2b9894dd5" />
<img width="1200" height="390" alt="image" src="https://github.com/user-attachments/assets/dfa7c2e7-0188-4ce2-a0a2-d0c4fd705d0b" />
<img width="800" height="403" alt="image" src="https://github.com/user-attachments/assets/c2b343b1-71b5-4f80-aae4-32abdd7f89ed" />
<img width="1200" height="500" alt="image" src="https://github.com/user-attachments/assets/7827e671-19f1-496f-9c75-fb9441292f2a" />
<img width="800" height="353" alt="image" src="https://github.com/user-attachments/assets/3ff4f41a-c1c7-4418-b5c3-fc4ae5d55302" />
<img width="1600" height="501" alt="image" src="https://github.com/user-attachments/assets/ff60f6f8-774e-46d8-9a4f-ca2c9e9c3604" />















   





  





