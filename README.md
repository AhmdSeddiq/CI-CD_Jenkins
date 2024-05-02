

Automated Build and Test with Jenkins on EC2 Instance

Project Setup

Tools Needed

EC2 instance on AWS with docker installed
Jenkins container
GitHub account
Gitbash on your local host

Installation and Configuration Guide
1. Prepare GitHub Repository:
Create or select a GitHub repository.
Apply the Git Flow model.
#using git bash clone your repo
$git clone "your repo"

$git flow init


$ vim Jenkinsfile


# paste these lines in the file
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


# Save and Exit


# Push the file to GitHub Repo
$ git add Jenkinsfile
$ git commit -m "Add Jenkinsfile"
$ git push <repo> develop

# check your develop branch on github repo
2. Installing Jenkins container on EC2:
$ sudo yum update -y
$ sudo yum install docker -y
$ sudo service docker start
$ docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
Note :
Add Inbound rule in the security group of the EC2 instance:

Type: Custom TCP
Port Range: 8080
source: 0.0.0.0/0
Accessing Jenkins:
Go to web browser and write : http://(ec2-public-ip)>:8080
run the following command inside your container to get Jenkins password
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Copy password to Jenkins tab and sign in
3. Configure Jenkins:
Install necessary plugins in Jenkins such as Git and Pipeline.
Connect Jenkins to GitHub.
Go to "Manage Jenkins" > "Manage Plugins" > "Available" and install "GitHub Integration Plugin".
Set up credentials in Jenkins for GitHub (username and token).
Create a new pipeline job.
Select "New Item", name your pipeline , and choose "Pipeline" as the type.
In the pipeline configuration, select "Pipeline script from SCM" and choose "Git" as the SCM.
Enter the repository URL and credentials.
Specify the branch to build .
In Build Triggers, Check "GitHub hook trigger for GITScm polling".
Save
4. Implement Webhooks for Continuous Integration:
Set up webhooks in GitHub to trigger Jenkins builds on push events.

In GitHub, go to your repository settings and select "Webhooks".
Add a new webhook:
Payload URL: http://:8080/github-webhook/
Content type: application/json
Select "Just the push event".
Ensure the webhook is active
With the webhook, Jenkins will trigger a new build every time changes are pushed to the connected branch.
5. Testing and Validation:
Push a change to the develop branch and verify Jenkins triggers a build.

Check the Jenkins dashboard for build status and output .....
