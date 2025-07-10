# Jenkins-Java-web-app
end-to-end Jenkins pipeline will automate the entire CI/CD process for a Java application, from code checkout to production deployment, using popular tools like SonarQube, Argo CD, Helm, and Kubernetes.

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


