# Full CI/CD AWS DevOps Project with CodeCommit, CodeBuild, CodeDeploy and CodePipeline 

![AWS Devops Project Architecture](https://github.com/shubhzzz19/aws-devops-cicd-project/assets/73218792/066a52b3-fa2d-447f-980a-6bce4595ecca)

This project contains a simple demo web application along with instructions for setting up a CI/CD pipeline using AWS services such as CodeCommit, CodeBuild, and CodeDeploy along with storage/artifact like AWS S3 and for hosting the application NGINX and AWS EC2 is used.

### Table of Contents
1. [Create AWS CodeCommit Repository](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#create-aws-codecommit-repository)
2. [Create IAM User and Role](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#create-iam-user-and-role)
3. [Give User Access to CodeCommit Repo](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#give-user-access-to-codecommit-repo)
4. [Clone Repository](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#clone-repository)
5. [CodeBuild Setup](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#codebuild-setup)
6. [S3 Bucket Setup](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#s3-bucket-setup)
7. [IAM Role for CodeDeploy](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#iam-role-for-codedeploy)
8. [EC2 Instance Setup](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#ec2-instance-setup)
9. [CodeDeploy Setup](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#codedeploy-setup)
10. [CI/CD Pipeline Setup](https://github.com/shubhzzz19/aws-devops-cicd-project/blob/main/README.md#cicd-pipeline-setup)

### Create AWS CodeCommit Repository
1. Go to the AWS CodeCommit service.
2. Create a new repository with the name (e.g., *demo-app*).
3. Clone the repository using the provided HTTPS URL.

### Create IAM User and Role
1. Go to the AWS IAM service.
2. Create a new IAM user (e.g., aws-devops-user) with the necessary permissions.
3. Note the console sign-in URL, username, and password.
4. Give the user permission to commit by attaching the AWSCodeCommitPowerUser policy.
 
### Give User Access to CodeCommit Repo
1. Go to IAM > Users > *<user-name>* > Security Credentials.
2. Under HTTPS Git credentials for AWS CodeCommit, generate credentials.
3. Use these credentials to clone the repo.

### Clone Repository
1. Clone the CodeCommit repository HTML URL

### CodeBuild Setup
1. Go to CodeBuild > Build Projects > Create build project.
2. Configure the project with source provider AWS CodeCommit and repository (*demo-app*).
3. Set up the build environment and buildspec file.
4. Create the build project and start the build.
   
### S3 Bucket Setup
1. Go to the AWS S3 service and create a bucket (e.g., *dev-demo-app-build*).
2. Update the CodeBuild project to use this bucket for artifacts.
3. Go to CodeBuild > Build projects > Select the build project > Click on *Edit* > Artifact > Select the S3 bucket

### IAM Role for CodeDeploy
1. Go to IAM > Roles > Create role.
2. Select AWS service as the trusted entity, use case EC2, and attach CodeDeploy-related policies i.e., *AmazonEC2FullAccess, AmazonEC2RoleforAWSCodeDeploy, AmazonS3FullAccess,AWSCodeDeployFullAccess, AWSCodeDeployRole, AmazonEC2RoleforAWSCodeDeployLimited*.

### EC2 Instance Setup
1. Create a AWS EC2 with any suitable OS
2. Select the AWS EC2 instance > Click on *Actions* > *Security* > *Modify IAM role* > Select *ec2-code-deploy* role > And then click on *Update IAM role*
    
### CodeDeploy Setup
1. Go to CodeDeploy > Create Application and Deployment Group.
2. Configure the deployment group with the created IAM role.
3. Install the CodeDeploy agent on your EC2 instance with help of below commands.
   - ```
     vi install.sh
     ```
     Paste the below scipt in the install.sh file
   - ```
     #!/bin/bash 
     # This installs the CodeDeploy agent and its prerequisites on Ubuntu 22.04.  
     sudo apt-get update 
     sudo apt-get install ruby-full ruby-webrick wget -y 
     cd /tmp 
     wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/releases/codedeploy-agent_1.3.2-1902_all.deb 
     mkdir codedeploy-agent_1.3.2-1902_ubuntu22 
     dpkg-deb -R codedeploy-agent_1.3.2-1902_all.deb codedeploy-agent_1.3.2-1902_ubuntu22 
     sed 's/Depends:.*/Depends:ruby3.0/' -i ./codedeploy-agent_1.3.2-1902_ubuntu22/DEBIAN/control 
     dpkg-deb -b codedeploy-agent_1.3.2-1902_ubuntu22/ 
     sudo dpkg -i codedeploy-agent_1.3.2-1902_ubuntu22.deb 
     systemctl list-units --type=service | grep codedeploy 
     sudo service codedeploy-agent status
     ```
     To run the file use the below command.
   - ` ./install.sh `

### CI/CD Pipeline Setup
1. Go to CodePipeline > Create Pipeline.
2. Configure the pipeline stages for CodeCommit, CodeBuild, and CodeDeploy.
3. Release the pipeline, and the application will be deployed automatically.

