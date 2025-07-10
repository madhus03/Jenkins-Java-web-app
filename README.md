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



  





