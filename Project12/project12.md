# ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)


### STEP 1 – Jenkins job enhancement

1. Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

```
sudo mkdir /home/ubuntu/ansible-config-artifact
```

2. Change permissions to this directory, so Jenkins could save files there 

```
chmod -R 0777 /home/ubuntu/ansible-config-artifact
```

![Screenshot from 2022-05-24 14-14-01](https://user-images.githubusercontent.com/34113547/170126074-fa5a002b-01ca-415f-9c1f-728f230d6b3d.png)


3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

![Screenshot from 2022-05-24 14-13-40](https://user-images.githubusercontent.com/34113547/170126151-2e5d560e-5429-45c5-905c-11720a716a1e.png)


4. Create a new Freestyle project and name it **save_artifacts**.

![Screenshot from 2022-05-24 14-16-00](https://user-images.githubusercontent.com/34113547/170126273-179dac12-de54-4972-861f-a512980292e2.png)


5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:
![Screenshot from 2022-05-24 14-19-17](https://user-images.githubusercontent.com/34113547/170126522-f0e46b0f-1df2-4a5b-8886-71208eb1e15c.png)

![Screenshot from 2022-05-24 14-19-33](https://user-images.githubusercontent.com/34113547/170126566-32a23ec9-17ca-4250-aec0-0c76b117a40d.png)


6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.
![Screenshot from 2022-05-24 14-19-43](https://user-images.githubusercontent.com/34113547/170126586-6ab9920b-4290-42e7-844a-e4eec3a6ef1f.png)

![Screenshot from 2022-05-24 14-22-42](https://user-images.githubusercontent.com/34113547/170126760-d5478a3d-0e3c-4abf-bace-916c09f57005.png)


7. Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside main branch).
![Screenshot from 2022-05-24 14-26-05](https://user-images.githubusercontent.com/34113547/170127119-1f8a0705-7cb7-4e3f-98ff-5f82442be8b2.png)

![Screenshot from 2022-05-24 14-28-19](https://user-images.githubusercontent.com/34113547/170127141-7ad0357c-03f2-482f-9b57-96b099407eaf.png)


### STEP 2 – Refactor Ansible code by importing other playbooks into site.yml

Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it **refactor**.

![Screenshot from 2022-05-24 14-31-59](https://user-images.githubusercontent.com/34113547/170127293-4398e688-bc5b-4430-9efc-a3146149da9d.png)

Let see code re-use in action by importing other playbooks.:

1. Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously. Dont worry, you will understand more what this means shortly.

2. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

3. Move common.yml file into the newly created static-assignments folder.

4. Inside site.yml file, import common.yml playbook.

![Screenshot from 2022-05-24 14-36-13](https://user-images.githubusercontent.com/34113547/170127467-fcbdbda3-23dc-4ac4-a120-b7d9248e4640.png)

The code above uses built in import_playbook Ansible module.

Your folder structure should look like this:

![Screenshot from 2022-05-24 14-38-05](https://user-images.githubusercontent.com/34113547/170127539-deab901d-22e8-4acd-bf7d-1166d5a12082.png)

5. Run ansible-playbook command against the dev environment
Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.
![Screenshot from 2022-05-24 14-39-18](https://user-images.githubusercontent.com/34113547/170128208-28040091-dafb-4cdb-9622-7d1621b72325.png)

* Update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:
![Screenshot from 2022-05-24 14-41-31](https://user-images.githubusercontent.com/34113547/170128423-7ae770d6-4a79-499c-94e0-f578e2434a43.png)

```
ansible-playbook -i inventory/dev.yml playbooks/site.yml
```
![Screenshot from 2022-05-24 15-29-35](https://user-images.githubusercontent.com/34113547/170128553-4b21055c-07bd-4caf-ae86-2ef86317189f.png)
![Screenshot from 2022-05-24 15-29-48](https://user-images.githubusercontent.com/34113547/170128576-072e2933-170a-4a2f-b1a0-f4429c88cb45.png)

* Make sure that wireshark is deleted on all the servers by running `wireshark --version`


### STEP 3 – Configure UAT Webservers with a role ‘Webserver’

We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT.

2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

* Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)

```
mkdir roles
cd roles
ansible-galaxy init webserver
```
![Screenshot from 2022-05-24 15-51-56](https://user-images.githubusercontent.com/34113547/170128728-45d5f6d4-edbb-43ff-ad4b-0ada39d1e330.png)

The entire folder structure should look like below, but if you create it manually – you can skip creating tests, files, and vars or remove them if you used ansible-galaxy
![Screenshot from 2022-05-24 15-57-00](https://user-images.githubusercontent.com/34113547/170128863-c25bb427-c126-49da-9cc0-351a56ff37d0.png)

After removing unnecessary directories and files, the roles structure should look like this:
![Screenshot from 2022-05-24 16-00-43](https://user-images.githubusercontent.com/34113547/170128953-0ba7f6f1-5de3-4d31-9e9d-be017f5573b9.png)

3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
```
![Screenshot from 2022-05-24 16-18-18](https://user-images.githubusercontent.com/34113547/170129159-bd2e4e88-aafc-4375-894d-233affcfaf6f.png)

4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

* Install and configure Apache (httpd service)
* Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
* Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
* Make sure httpd service is started

Your main.yml may consist of following tasks:

![Screenshot from 2022-05-24 16-30-20](https://user-images.githubusercontent.com/34113547/170129483-603a59f9-b786-49b4-b60e-1cac91c63a7b.png)


### STEP 4 – Reference ‘Webserver’ role

Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.
![Screenshot from 2022-05-24 16-32-18](https://user-images.githubusercontent.com/34113547/170129921-7fd9919f-eb90-4edf-9c68-90bd2f7bdb35.png)

Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml.

So, we should have this in site.yml
![Screenshot from 2022-05-24 16-34-06](https://user-images.githubusercontent.com/34113547/170129939-3c5f2657-5a3d-44c2-a63c-6b7ba1412090.png)


### STEP 5 – Commit & Test

Commit your changes, create a Pull Request and merge them to main branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.
![Screenshot from 2022-05-24 16-37-41](https://user-images.githubusercontent.com/34113547/170130348-c225f418-87d2-44be-a067-e8e20182ef49.png)

![Screenshot from 2022-05-24 16-39-19](https://user-images.githubusercontent.com/34113547/170130532-ba426c6c-81a1-4f2e-b529-fb4994b85331.png)

![Screenshot from 2022-05-24 16-39-36](https://user-images.githubusercontent.com/34113547/170130577-6cd56aa2-c3a8-452c-aae5-f0604561c8f1.png)

Now run the playbook against your uat inventory and see what happens:

```
sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml
```
![Screenshot from 2022-05-24 20-47-55](https://user-images.githubusercontent.com/34113547/170130807-af8ebdc9-fe39-4830-bec3-664dba033976.png)

![Screenshot from 2022-05-24 20-48-03](https://user-images.githubusercontent.com/34113547/170130837-34115205-f662-4901-9125-95fe62001071.png)

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

http://Web1-UAT-Server-Public-IP-or-Public-DNS-Name/index.php

or

http://Web1-UAT-Server-Public-IP-or-Public-DNS-Name/index.php
  
![Screenshot from 2022-05-24 20-49-45](https://user-images.githubusercontent.com/34113547/170131115-a110f746-eb26-4359-9d98-7571c8c530bd.png)

![Screenshot from 2022-05-24 20-49-51](https://user-images.githubusercontent.com/34113547/170131648-9886d5a2-3ebd-4fd5-8f22-cd01aa216f35.png)

Your Ansible architecture now looks like this:

![project12_architecture](https://user-images.githubusercontent.com/34113547/170131245-96f27c0f-1106-4c0d-9f72-89e1ff32268e.png)
