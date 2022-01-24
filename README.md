# Integrating Ansible with Jenkins in a CI/CD process

In this demo, we will explore the CI/CD process using Jenkins and Ansible. We will deploy a sample flask application using Ansible. For this, first we will build a docker image for the Flask Application in a build server and then deploy the containers for this application in a Test environment. 

Any new commits which are applied to the application by the development team will be automatically build and deployed to the test environment server using Jenkins.

## Features

- Build a docker image for the flask application in Build server using ansible.
- Deploy a containerized application from the docker image in Test server using ansible.
- Automatically build and deploy the application based on new commits using Github.

## Pre-Requests

- Install Ansible  on the master machine.
```
wget https://releases.hashicorp.com/packer/1.7.5/packer_1.7.5_linux_amd64.zip
unzip packer_1.7.5_linux_amd64.zip
mv packer /usr/bin/
```
- Install Jenkins on the master machine. (Once installed Jenkins can be accessed using http://<public-Ip>:8080)
```
amazon-linux-extras install epel -y
yum install java-1.8.0-openjdk-devel -y
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum -y install jenkins
systemctl enable jenkins
```
- Github account.
- DockerHub account.
- Two Ec2 Instances for building image and deploying container.

## Tools used 

- Ansible
- Docker
- Git
- Jenkins

## Architecture

![Untitled (1)](https://user-images.githubusercontent.com/93197553/150859874-bcf7f0dc-08da-4d08-b650-1df0edff2177.png)


## Variables Used

```
repo_url: "https://github.com/Freeda-F/FlaskApp-demo.git"  #-------application URL in github------#
repo_dest: "/var/repository"  #-------repository destination------#
image_name: "freedafrancis/flaskapp-demo"  #-------docker-image name------#
docker_username: "freedafrancis" #-------dockerhub username------#
docker_password: "*********" #-------dockerhub password------#
```

## Provisioning

#### Clone the repository

```
git clone git@github.com:Freeda-F/CICD-Using-Jenkins-Ansible.git
```

#### Jenkins Configuration

1. Login to Jenkins (http://ip:8080/) and install the required plugins.

Manage Jenkins --> Manage Plugins and Search for ansible plugin in the available section and install the same.
  
![image](https://user-images.githubusercontent.com/93197553/150861966-91eb05e8-dcb2-4246-af13-a74ef4e2ea4a.png)

2. Configure Ansible plugin for integration.

Manage Jenkins --> Global Tool Configuration. Here, update the executable path to ansible and save the changes.
  
![image](https://user-images.githubusercontent.com/93197553/150862576-cd820917-d795-4975-80a8-1834ccae17b6.png)

3. Create a free style project from Dashboard --> New Item and update the Git repository url under Source Code Management and SAVE.

![image](https://user-images.githubusercontent.com/93197553/150863316-f197780e-0fa0-44f3-ab95-157299b566d3.png)

4. Under build-trigger section, choose 'GitHub hook trigger for GITScm polling' for using Webhook in Github.

![image](https://user-images.githubusercontent.com/93197553/150863559-6c0ad297-a433-4b80-ac03-84185d77a926.png)
  
5. update build procedure from Build Environment as 'invoke the ansible-playbook' and update the ansible palybook file location.

![image](https://user-images.githubusercontent.com/93197553/150863816-b072648d-85b3-4f91-9dc6-ac8652d353c6.png)
  
Jenkins configuration has been completed now.

#### WebHook configuration in GitHub

After configuring the anisble from jenkins like above then enable git webhooks to alert the playbook to trigger.

![image](https://user-images.githubusercontent.com/93197553/150864381-0313dedb-75f9-4a61-8f5b-e201ca3cdc58.png)

## Result

Now, whenever a commit is made on this git repository the application's image is build and deployed into a docker container automatically.
