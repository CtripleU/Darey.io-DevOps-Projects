# CONTINUOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE
## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

### Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

![2](https://user-images.githubusercontent.com/34113547/166131266-b4f9beaa-bd83-4c87-bf52-62b31bb812ad.png)


### Step 1: Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

![Screenshot from 2022-05-01 05-49-22](https://user-images.githubusercontent.com/34113547/166133621-fa3658e9-787a-4755-8787-bf4e90598c2c.png)

Install JDK (since Jenkins is a Java-based application)

![Screenshot from 2022-05-01 05-50-25](https://user-images.githubusercontent.com/34113547/166133629-78341a14-6f4b-495b-90c9-90d36c12e820.png)


2. Install Jenkins

Run 
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```
![Screenshot from 2022-05-01 05-52-51](https://user-images.githubusercontent.com/34113547/166133639-a2571301-fe2d-4500-870c-f45dae7bd4b8.png)


3. Make sure Jenkins is up and running

`sudo systemctl status jenkins`

![Screenshot from 2022-05-01 05-53-42](https://user-images.githubusercontent.com/34113547/166133642-c5d82fd2-6a9c-4f5d-9a97-fa94201922c8.png)


4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![Screenshot from 2022-05-01 05-56-06](https://user-images.githubusercontent.com/34113547/166133664-e19aa3dd-8480-4733-af33-449318ec4051.png)


5. Perform initial Jenkins setup

From your browser access http://35.170.59.97:8080

![Screenshot from 2022-05-01 05-57-46](https://user-images.githubusercontent.com/34113547/166133675-d73b7c9f-86ac-4de1-9e94-97e7f88ff3c0.png)

Retrieve the default admin password from your server
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![Screenshot from 2022-05-01 05-58-55](https://user-images.githubusercontent.com/34113547/166133689-23ff544f-94e1-491a-b7ab-cac9cbbb73aa.png)

Create an admin user

![Screenshot from 2022-05-01 06-01-30](https://user-images.githubusercontent.com/34113547/166133701-afd3981a-6df8-4568-854c-c320df105d77.png)

![Screenshot from 2022-05-01 06-02-00](https://user-images.githubusercontent.com/34113547/166133707-190f1dd7-69a5-4b87-8c96-945b2045ed23.png)



### Step 2: Configure Jenkins to retrieve source codes from GitHub using Webhooks

1. Enable webhooks in your GitHub repository settings

![Screenshot from 2022-05-01 06-20-29](https://user-images.githubusercontent.com/34113547/166133715-44fc1dea-b600-4384-b3d1-9804b9be2502.png)

![Screenshot from 2022-05-01 06-20-42](https://user-images.githubusercontent.com/34113547/166133722-3862df4a-beb0-4962-814c-8b571a8afc1d.png)


2. Go to Jenkins web console and create a "Freestyle project"

![Screenshot from 2022-05-01 06-22-03](https://user-images.githubusercontent.com/34113547/166133731-1dc10bcf-e0ed-41a6-92bc-64b7482b2865.png)

![Screenshot from 2022-05-01 06-26-30](https://user-images.githubusercontent.com/34113547/166133738-39a97e21-e285-4acb-9fb4-3896df0f6c53.png)

Save the job and click the Build Now button. If you have configured everything correctly, the build will be successful.

![Screenshot from 2022-05-01 06-29-32](https://user-images.githubusercontent.com/34113547/166133745-166896fc-a1f7-4889-95e7-82326cee079e.png)

![Screenshot from 2022-05-01 06-31-54](https://user-images.githubusercontent.com/34113547/166133748-4c63a973-2de2-4854-bb30-e21426fbd5af.png)

From the screenshot, the build is successful and is seen under #1


3. Configure triggering the job from GitHub webhook

Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

Change any file in the GitHub repository and push the changes to the main branch.

![Screenshot from 2022-05-01 06-34-56](https://user-images.githubusercontent.com/34113547/166133764-4ff16a59-d169-4071-b66a-41e6a60292e0.png)

![Screenshot from 2022-05-01 06-36-11](https://user-images.githubusercontent.com/34113547/166133771-a40ff696-9d04-407d-98f3-6e7853e6f011.png)

![Screenshot from 2022-05-01 06-39-11](https://user-images.githubusercontent.com/34113547/166133776-1d5e805e-16d6-4bec-8751-a1c2fb12f65e.png)

The automated Jenkins job that receives files from GitHub by webhook trigger was successful.

By default, the artifacts are stored on Jenkins server locally

Run `ls /var/lib/jenkins/jobs/tooling/builds/2/archive/` to view them

![Screenshot from 2022-05-01 07-42-09](https://user-images.githubusercontent.com/34113547/166133808-4cc942cf-a983-4b8d-a793-2d4ae71f5de4.png)


## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH


### Step 3: Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

1. Install "Publish Over SSH" plugin

![Screenshot from 2022-05-01 06-44-47](https://user-images.githubusercontent.com/34113547/166133822-08f2cb04-9fac-446e-9b8d-c270c56da917.png)

2. Configure the job/project to copy artifacts over to NFS server
On main dashboard select "Manage Jenkins" and choose "Configure System"

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

* Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
* Name
* Hostname
* Username
* Remote directory

![Screenshot from 2022-05-01 07-21-17](https://user-images.githubusercontent.com/34113547/166133848-ff8fce2d-92f3-4749-aac0-c79a5aa9394d.png)

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

![Screenshot from 2022-05-01 07-21-25](https://user-images.githubusercontent.com/34113547/166133853-72819b95-8dd8-472e-b581-2b6f533d7831.png)

Add another Post-build action to the project/job to send all files probuced by the build into the defined remote directory. Use ** to copy all files and directories

![Screenshot from 2022-05-01 06-55-23](https://user-images.githubusercontent.com/34113547/166134089-29364d17-5144-486a-901c-f88600fd09a1.png)

Save this configuration and change something the repository

![Screenshot from 2022-05-01 07-27-58](https://user-images.githubusercontent.com/34113547/166133858-d30e2e90-bdd9-4298-93ed-56abd6fc7712.png)

![Screenshot from 2022-05-01 07-29-49](https://user-images.githubusercontent.com/34113547/166133883-72b9b430-7e3a-43f9-a75d-1f008a953259.png)
