# Automated Build and Test with Jenkins on EC2 Instance



## Project Setup

### Tools Needed
- EC2 instance on AWS with docker installed
- Jenkins container 
- GitHub account
- Gitbash on your local host

## Installation and Configuration Guide
### 1. *Prepare GitHub Repository:*
   - Create or select a GitHub repository.
   - Apply the Git Flow model.
bash
#using git bash clone your repo
$git clone "your repo"

$git flow init


### 2. *pushing java code that you want to build which contain:*
   - java app code
   - groovy script that builds the artifact
   - Jenkinsfile

```
    pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

```




### 3. *Installing Jenkins container on EC2:*

On the EC2 Terminal

#### First, update your existing list of packages:

```
$ sudo apt update
```
#### Next, install a few prerequisite packages which let apt use packages over HTTPS:
```
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
#### Then add the GPG key for the official Docker repository to your system:
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
#### Add the Docker repository to APT sources:
```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
#### This will also update our package database with the Docker packages from the newly added repo.

#### Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:
```
$ apt-cache policy docker-ce
```
#### Finally, install Docker:
```
sudo apt install docker-ce
```
#### After the installation you going to run jenkins as a container with a composed port 8080 and persistent volume
```
$ docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

### Note :
*Add Inbound rule in the security group of the EC2 instance:*
- Type: Custom TCP
- Port Range: 8080
- source: 0.0.0.0/0

### Accessing Jenkins:
- Go to web browser and write : http://(ec2-elastic-ip)>:8080
- After getting in to the jenkins container run the following command inside your container to get Jenkins password:
```
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- Copy password to Jenkins tab and sign in


### 4. *Configure Jenkins:*
   - *Install necessary plugins in Jenkins such as Git and Pipeline.*
   - *Connect Jenkins to GitHub.*
        - Go to "Manage Jenkins" > "Manage Plugins" > "Available" and install "GitHub Integration Plugin".
     - Set up credentials in Jenkins for GitHub (username and password).
   - *Create a new pipeline job.*
     - Select "New Item", name your pipeline , and choose "Pipeline" as the type.
     - In the pipeline configuration, select "Pipeline script from SCM" and choose "Git" as the SCM.
     - Enter the repository URL and credentials.
     - Specify the branch to build .
     - In Build Triggers, Check *"GitHub hook trigger for GITScm polling"*.
     - Save

### 5. *Implement Webhooks for Continuous Integration:*
*Set up webhooks in GitHub to trigger Jenkins builds on push events.*
- In GitHub, go to your repository settings and select *"Webhooks"*.
- *Add a new webhook:*
   - Payload URL: http://<your-jenkins-url>:8080/github-webhook/
   - Content type: *application/json*
   - Select *"Just the push event"*.
  
### *With the webhook, Jenkins will trigger a new build every time changes are pushed to the connected branch.*

### 6. *Testing and Validation:*
   - Push a change to the develop branch and verify Jenkins triggers a build.
   
