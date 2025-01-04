Apache Website Deployment with Jenkins CI/CD
Project Overview
This project demonstrates how to set up a continuous integration and deployment (CI/CD) pipeline using Jenkins to automatically deploy a website to an Apache web server on Amazon Linux 2. The pipeline includes automatic deployments triggered by GitHub webhooks, backup management, and failure recovery mechanisms.
Prerequisites

Amazon Linux 2 EC2 instance
Root or sudo access
GitHub account
Basic understanding of Linux commands
Basic understanding of Jenkins pipelines

Installation Steps
1. Apache Web Server Setup
bashCopysudo yum update
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl status httpd
2. Git Installation and Configuration
bashCopysudo yum install git -y
git config --global user.name "your name"
git config --global user.email "your email@gmail.com"
3. Jenkins Installation
bashCopy# Add Jenkins repository
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Install Java
sudo amazon-linux-extras enable java-openjdk11
sudo yum install java-17-amazon-corretto-devel -y

# Install Jenkins
sudo yum install jenkins -y

# Configure Java
export JAVA_HOME=/usr/lib/jvm/java-17-amazon-corretto.x86_64
source ~/.bash_profile

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
Jenkins Configuration
Initial Setup

Access Jenkins at http://<your-ec2-instance-public-ip>:8080
Retrieve initial admin password: sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Install suggested plugins
Create admin user

Required Plugins

GitHub Integration
Pipeline
Git
Workspace Cleanup

GitHub Webhook Configuration

In your GitHub repository:

Go to Settings > Webhooks
Add webhook:

Payload URL: http://<your-ec2-instance-public-ip>:8080/github-webhook/
Content Type: application/json





Pipeline Configuration
Jenkins Pipeline Setup

Create new Pipeline job
Enable GitHub hook trigger
Add pipeline script (provided in pipeline-script.md)

Security Configuration
Add the following to sudoers file using sudo visudo:
bashCopyjenkins ALL=(ALL) NOPASSWD: /usr/bin/mkdir -p /var/www/html
jenkins ALL=(ALL) NOPASSWD: /usr/bin/rm -rf /var/www/html/*
jenkins ALL=(ALL) NOPASSWD: /usr/bin/cp -r * /var/www/html/
jenkins ALL=(ALL) NOPASSWD: /bin/chown -R apache apache /var/www/html
jenkins ALL=(ALL) NOPASSWD: /bin/chmod -R 755 /var/www/html
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl start httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl stop httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl reload httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl status httpd
jenkins ALL=(ALL) NOPASSWD: /bin/mkdir
jenkins ALL=(ALL) NOPASSWD: /bin/cp -r
jenkins ALL=(ALL) NOPASSWD: /bin/rm -rf
jenkins ALL=(ALL) NOPASSWD: /bin/chown -R
jenkins ALL=(ALL) NOPASSWD: /bin/chmod -R
Pipeline Features

Automated deployment on GitHub push
Backup of current deployment before updates
Automatic rollback on deployment failure
Permission management for web files
Apache service health checks

Directory Structure
Copy/var/www/
├── html/          # Web root directory
└── backup/        # Backup directory for rollbacks
Troubleshooting

If Jenkins fails to start, check Java installation:
bashCopyjava -version
echo $JAVA_HOME

If webhook isn't triggering, verify:

Webhook URL is correct
Jenkins is accessible from GitHub
GitHub plugin is properly configured



Contributing
Feel free to submit issues and enhancement requests!
License
MIT License
Author
[Your Name]
