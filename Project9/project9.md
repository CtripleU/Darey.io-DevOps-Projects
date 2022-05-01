# CONTINUOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE
## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

### Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

![2](https://user-images.githubusercontent.com/34113547/166131266-b4f9beaa-bd83-4c87-bf52-62b31bb812ad.png)


### Step 1: Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

Install JDK (since Jenkins is a Java-based application)

2. Install Jenkins

Run 
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

3. Make sure Jenkins is up and running

`sudo systemctl status jenkins`

4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

5. Perform initial Jenkins setup

From your browser access http://35.170.59.97:8080

Retrieve the default admin password from your server
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Create an admin user and you will get your Jenkins server address.

The installation is completed!


### Step 2: Configure Jenkins to retrieve source codes from GitHub using Webhooks

1. Enable webhooks in your GitHub repository settings

2. Go to Jenkins web console and create a "Freestyle project"

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

Save the job and click the Build Now button. If you have configured everything correctly, the build will be successful.

From the screenshot, the build is successful and is seen under #1

3. Configure triggering the job from GitHub webhook

Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

Change any file in the GitHub repository and push the changes to the main branch.


The automated Jenkins job that receives files from GitHub by webhook trigger was successful.

By default, the artifacts are stored on Jenkins server locally

Run `ls /var/lib/jenkins/jobs/tooling/builds/2/archive/` to view them

## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

### Step 3: Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

1. Install "Publish Over SSH" plugin

2. Configure the job/project to copy artifacts over to NFS server
On main dashboard select "Manage Jenkins" and choose "Configure System"

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

* Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
* Name
* Hostname
* Username
* Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server


Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

Add anothey Post-build action to the project/job to send all files probuced by the build into the defined remote directory. Use ** to copy all files and directories
