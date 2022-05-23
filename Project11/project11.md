# Ansible – Automate Project 7 to 10

### STEP 1 - Install and configure Ansible on EC2 instance

1. Update the Name tag on your Jenkins EC2 Instance from Project 9 to **Jenkins-Ansible**. This server will be used to run playbooks

2. Create a new repository on GitHub with the name **ansible-config-mgt**

3. Install Ansible

```
sudo apt update

sudo apt install ansible
```

![Screenshot from 2022-05-04 21-17-26](https://user-images.githubusercontent.com/34113547/169897315-9b2a31ff-178a-473a-ba2e-e3e41aaea723.png)


![Screenshot from 2022-05-04 21-18-16](https://user-images.githubusercontent.com/34113547/169897370-41df8185-ab3d-43d2-bc79-b33fe342a0c9.png)


4. Configure Jenkins build job to save your repository content every time you change it 

* Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

![Screenshot from 2022-05-22 20-42-41](https://user-images.githubusercontent.com/34113547/169897821-c938c752-22bf-47ce-be3f-db5479f228d6.png)

* Configure Webhook in GitHub and set webhook to trigger ansible build.

![Screenshot from 2022-05-22 20-41-38](https://user-images.githubusercontent.com/34113547/169897678-772fa9c2-d74e-401d-80e2-23d4fa72f10d.png)

* Configure a Post-build job to save all (**) files, like you did it in Project 9.

![Screenshot from 2022-05-22 20-47-22](https://user-images.githubusercontent.com/34113547/169898005-fa446465-ec07-417f-b16b-f1ea0c9e2bde.png)

![Screenshot from 2022-05-22 20-49-54](https://user-images.githubusercontent.com/34113547/169898024-2479e891-96d8-4bf5-9d54-c5a48658f015.png)

![Screenshot from 2022-05-22 20-54-55](https://user-images.githubusercontent.com/34113547/169898095-e7520e9f-df89-4f72-8c57-14578b2015b6.png)

5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following the /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory.

![Screenshot from 2022-05-22 20-57-55](https://user-images.githubusercontent.com/34113547/169898345-5d727b3e-9ff0-4826-bf4b-fa77cce0e3e6.png)

![Screenshot from 2022-05-22 20-58-49](https://user-images.githubusercontent.com/34113547/169898396-51297f22-a358-4748-9eae-d7e02655d402.png)

The setup now looks like this:
![neww](https://user-images.githubusercontent.com/34113547/169898516-8a248d3d-1eb2-4b0e-9c38-f5df7183ba54.png)

* Allocate an Elastic IP to the Jenkins-Ansible server to avoid having to reconfigure GitHub webhook to a new IP address every time you stop/start your Jenkins-Ansible server

![Screenshot from 2022-05-22 21-03-11](https://user-images.githubusercontent.com/34113547/169900671-b5b58ba6-3759-44b1-bf63-15bfebac8544.png)

![Screenshot from 2022-05-22 21-11-45](https://user-images.githubusercontent.com/34113547/169900750-72f84539-b9fe-47b1-b28b-780e5c04e6a5.png)


### STEP 2 - Prepare your development environment using Visual Studio Code

Install VS Code and set it up to connect to the ansible-config-mgt repository created earlier.

Clone the ansible-config-mgt repo to your Jenkins-Ansible instance

`git clone <ansible-config-mgt repo link>`

![Screenshot from 2022-05-23 22-24-12](https://user-images.githubusercontent.com/34113547/169900105-688b531b-0630-4bb1-a665-9bcfce5ed8ff.png)


### STEP 3 - Begin Ansible development

* In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.
![Screenshot from 2022-05-22 21-17-24](https://user-images.githubusercontent.com/34113547/169901010-08c49881-38c9-454f-a474-548a64fe592a.png)

* Checkout the newly created feature branch to your local machine and start building your code and directory structure
![Screenshot from 2022-05-22 22-43-26](https://user-images.githubusercontent.com/34113547/169901140-10082a2b-4571-4a3c-810c-f32e4f2666d3.png)

* Create a directory and name it playbooks – it will be used to store all your playbook files.
![Screenshot from 2022-05-22 22-43-43](https://user-images.githubusercontent.com/34113547/169901557-ec6bb65b-c773-4aad-a32c-6ef23f36138f.png)

* Create a directory and name it inventory – it will be used to keep your hosts organised.
![Screenshot from 2022-05-22 22-45-04](https://user-images.githubusercontent.com/34113547/169901595-8a3b15d3-6751-4eda-a173-029223ea2a50.png)

* Within the playbooks folder, create your first playbook, and name it common.yml
![Screenshot from 2022-05-22 22-45-51](https://user-images.githubusercontent.com/34113547/169901667-ecd7bec5-809e-4ef4-b468-4a3e2ffd315d.png)

* Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
![Screenshot from 2022-05-22 22-48-22](https://user-images.githubusercontent.com/34113547/169901702-4b52b5d6-89e4-4a0d-80df-faa343b03a36.png)

![Screenshot from 2022-05-23 00-13-27](https://user-images.githubusercontent.com/34113547/169902495-54fee4dc-c929-47db-8768-bcad1b7855c6.png)


### STEP 4 – Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. We need to have a way to organize our hosts in such an Inventory.

Since Ansible uses TCP port 22 by default, it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

![Screenshot from 2022-05-23 14-46-50](https://user-images.githubusercontent.com/34113547/169904083-1bc12171-9225-450e-bde0-b61b1ed188a9.png)

Do this for all servers.

Update your inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
![Screenshot from 2022-05-23 13-57-41](https://user-images.githubusercontent.com/34113547/169904420-d6aa7638-25d8-430a-99cd-e2ce157fbdb9.png)


### STEP 5 – Create a Common Playbook

It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file:

![Screenshot from 2022-05-23 17-35-18](https://user-images.githubusercontent.com/34113547/169904660-1da1da5b-f268-4565-81ff-3804c0ffa396.png)


### STEP 6 – Update GIT with the latest code

* Commit your code into GitHub:

* Create a Pull request (PR)
![Screenshot from 2022-05-23 17-39-55](https://user-images.githubusercontent.com/34113547/169905713-960a14e5-b32d-4591-b249-96e66f947587.png)

![Screenshot from 2022-05-23 17-40-04](https://user-images.githubusercontent.com/34113547/169905772-d471be6e-c7c0-4139-b60f-37197d663f42.png)

* Wear a hat of another developer for a second, and act as a reviewer. Merge the code to the main branch.
![Screenshot from 2022-05-23 17-40-26](https://user-images.githubusercontent.com/34113547/169905817-f76b48df-3db5-43db-ae2e-6079af47ce69.png)

* Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.
![Screenshot from 2022-05-23 17-42-21](https://user-images.githubusercontent.com/34113547/169905884-ed230c12-84ad-4e58-a6f6-2350040a8ccb.png)

* Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![Screenshot from 2022-05-23 17-43-36](https://user-images.githubusercontent.com/34113547/169905928-ad9f638e-dcc7-4334-9e45-4f598eab6648.png)

![Screenshot from 2022-05-23 17-44-25](https://user-images.githubusercontent.com/34113547/169905968-b6f502f4-24e5-4683-8ccc-9f9f356bde78.png)


### STEP 7 – Run first Ansible test

`cd ansible-config-mgt`
`ansible-playbook -i inventory/dev.yml playbooks/common.yml`

![Screenshot from 2022-05-23 19-22-07](https://user-images.githubusercontent.com/34113547/169906283-0eaef4f0-7d9d-48a0-9034-1446f00ed7ba.png)

You can verify that wireshark has been installed by running `wireshark --version` on each server


Additional Ansible Exercises:

* Change timezone on servers

![Screenshot from 2022-05-23 19-39-48](https://user-images.githubusercontent.com/34113547/169906747-33823365-1d33-4079-bb6e-f53c8fbe2c20.png)

![Screenshot from 2022-05-23 19-40-05](https://user-images.githubusercontent.com/34113547/169906817-5d4c193a-742e-47c0-9f9b-280b9328c254.png)
