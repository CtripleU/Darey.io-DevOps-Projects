# DEVOPS TOOLING WEBSITE SOLUTION

This project makes use of the following tools/technologies:
1. Jenkins 
2. Kubernetes 
3. Jfrog Artifactory 
4. Rancher
5. Grafana
6. Prometheus
7. Kibana

The project will consist of:
Infrastructure: AWS
Webserver Linux: Red Hat Enterprise Linux 8
Database Server: Ubuntu 20.04 + MySQL
Storage Server: Red Hat Enterprise Linux 8 + NFS Server
Programming Language: PHP
Code Repository: GitHub

### Step 1: Prepare NFS Server
* Spin up a new EC2 instance with RHEL Linux 8 Operating System

![Screenshot from 2022-04-30 10-17-12](https://user-images.githubusercontent.com/34113547/166100691-cd1b64d4-94de-413f-98e5-8b3946153204.png)

![Screenshot from 2022-04-30 10-56-37](https://user-images.githubusercontent.com/34113547/166100707-8e444bc7-3307-4cd7-b99c-773ee2772bb9.png)

* Configure LVM on the Server

Note: Instead of formating the disks as ext4 you will have to format them as xfs. There should be 3 Logical Volumes. lv-opt lv-apps, and lv-logs
Create mount points on /mnt directory for the logical volumes as follow:
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

![Screenshot from 2022-04-30 11-53-26](https://user-images.githubusercontent.com/34113547/166100774-8d59f1c9-1066-41a2-92d5-afe2961ff078.png)

![Screenshot from 2022-04-30 10-56-37](https://user-images.githubusercontent.com/34113547/166100923-2e93337b-0c94-4e7a-89d5-ccba6a58e525.png)

![Screenshot from 2022-04-30 11-10-45](https://user-images.githubusercontent.com/34113547/166100934-8a3611ba-f568-477d-bf52-5624625fce91.png)

![Screenshot from 2022-04-30 11-12-03](https://user-images.githubusercontent.com/34113547/166100941-7faf6945-d2ca-45e7-aa45-bda4cc44c6f7.png)

![Screenshot from 2022-04-30 11-12-15](https://user-images.githubusercontent.com/34113547/166100944-f30cca64-a0fa-4ebe-89c7-a9172d21f9f3.png)

![Screenshot from 2022-04-30 11-12-57](https://user-images.githubusercontent.com/34113547/166100951-b2ea327e-308a-4bf0-a3bd-8836b10d7236.png)

![Screenshot from 2022-04-30 11-14-48](https://user-images.githubusercontent.com/34113547/166100964-a44c3b46-9d3e-4f41-ae69-e2fd7571e87f.png)

![Screenshot from 2022-04-30 11-16-49](https://user-images.githubusercontent.com/34113547/166100968-6b25a62d-74c6-4150-a83f-e40ce9fbbda5.png)

![Screenshot from 2022-04-30 11-21-19](https://user-images.githubusercontent.com/34113547/166100996-78e736ba-888a-48e7-9c7f-a1ab880e34ab.png)

![Screenshot from 2022-04-30 11-23-56](https://user-images.githubusercontent.com/34113547/166101003-0f2a6df7-e174-4f12-9d43-a17b40fa376d.png)

![Screenshot from 2022-04-30 11-36-17](https://user-images.githubusercontent.com/34113547/166101010-1b6c5d6d-435e-495f-b369-dd108fdff9ee.png)

![Screenshot from 2022-04-30 11-37-52](https://user-images.githubusercontent.com/34113547/166101018-961d1a9f-6782-4e1c-863d-37b7d8bbb5e9.png)

![Screenshot from 2022-04-30 11-41-23](https://user-images.githubusercontent.com/34113547/166101028-eda273e0-0d07-4015-b9ec-e69594ee0779.png)

![Screenshot from 2022-04-30 11-42-25](https://user-images.githubusercontent.com/34113547/166101033-0d1f7fca-134c-498e-928b-de48fdd5b2f6.png)

![Screenshot from 2022-04-30 11-48-33](https://user-images.githubusercontent.com/34113547/166101046-b4a9ace4-1e4f-456f-a4c7-391081631773.png)

![Screenshot from 2022-04-30 11-49-25](https://user-images.githubusercontent.com/34113547/166101053-10f9c5b8-300a-477d-a722-4401fd705eb1.png)


* Install NFS server and configure it to start on reboot. Make sure it is up and running

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```
![Screenshot from 2022-04-30 12-18-56](https://user-images.githubusercontent.com/34113547/166120997-8b1cb9b8-d888-4d94-983d-e79c177a9ed1.png)

* Set up permission that will allow the Web servers to read, write and execute files on NFS

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```

* Configure access to NFS for clients within the instance's subnet CIDR 

Run `sudo vi /etc/exports`

```
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
```
![Screenshot from 2022-04-30 12-31-58](https://user-images.githubusercontent.com/34113547/166121052-b03f6b50-8061-4623-a3be-b1d7a389354d.png)

Export the filesystem to the clients`sudo exportfs -arv`

![Screenshot from 2022-04-30 12-33-20](https://user-images.githubusercontent.com/34113547/166121069-7bc5fa70-9d9f-473f-b59d-ffe57227027d.png)

* Check which port is used by NFS and open it using Security Groups
`rpcinfo -p | grep nfs`

![Screenshot from 2022-04-30 12-34-34](https://user-images.githubusercontent.com/34113547/166121085-473e64f7-767b-45fa-b230-6e882b062376.png)

![Screenshot from 2022-04-30 12-34-34](https://user-images.githubusercontent.com/34113547/166121092-db19402d-9af5-426f-be1b-893dad8b97ea.png)

Restart NFS server `sudo systemctl restart nfs-server`



### STEP 2: Configure the Darabase Server
![Screenshot from 2022-04-30 12-51-35](https://user-images.githubusercontent.com/34113547/166121104-8d9f0f60-8dde-4e54-8ff2-cf02ce47ed87.png)

![Screenshot from 2022-04-30 12-52-22](https://user-images.githubusercontent.com/34113547/166121151-374f6177-4c3c-484c-9f39-c96ac3ceb523.png)

`CREATE DATABASE tooling;`
`CREATE USER 'webaccess'@'%' IDENTIFIED BY 'password';`
`GRANT ALL PRIVILEGES ON TOOLING.* to 'webaccess'@'%';`
`FLUSH PRIVILEGES;`

![Screenshot from 2022-04-30 13-04-48](https://user-images.githubusercontent.com/34113547/166121164-a4c3527f-b00c-437a-9471-63fdb7720a93.png)



### Step 3: Prepare the Web Servers

* Create a bash script **shell.sh** to automate the set up of the webservers
```
#!/bin/bash

sudo yum install nfs-utils nfs4-acl-tools -y
sudo mkdir -p /var/www
sudo mount -t nfs -o rw,nosuid 172.31.26.247:/mnt/apps /var/www
sudo chmod 766 /etc/fstab
sudo echo "172.31.26.247:/mnt/apps /var/www     xfs defaults 0 0" >> /etc/fstab

sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y

sudo dnf module reset php -y

sudo dnf module enable php:remi-7.4 -y

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd -y

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
```

`sudo vi shell.sh && sudo chmod +x shell.sh`
`sudo su`
`./shell.sh`

* Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps.

![Screenshot from 2022-04-30 17-48-10](https://user-images.githubusercontent.com/34113547/166121216-9d5ec1da-17a9-40e7-aa8e-de6c4c319df6.png)

![Screenshot from 2022-04-30 17-48-20](https://user-images.githubusercontent.com/34113547/166121226-9075159e-0596-4244-98b8-e8e3c6c20dc2.png)


* Clone the repository in the webserver 
`sudo yum install git`
`git clone https://github.com/darey-io/tooling.git`

![Screenshot from 2022-04-30 18-03-10](https://user-images.githubusercontent.com/34113547/166121238-ff1ce62f-9ea8-4b20-a66a-8c37bdd808da.png)

![Screenshot from 2022-04-30 18-06-18](https://user-images.githubusercontent.com/34113547/166121296-e349d82a-e26f-4662-9c11-de3349323863.png)

![Screenshot from 2022-04-30 18-08-44](https://user-images.githubusercontent.com/34113547/166121305-237671ad-2584-444b-84ec-f43a70da3ed2.png)

* Install mysql on both webservers
![Screenshot from 2022-04-30 18-09-32](https://user-images.githubusercontent.com/34113547/166121319-fea9f8ce-7e20-4eed-bfde-6b01ef758654.png)

![Screenshot from 2022-04-30 18-09-58](https://user-images.githubusercontent.com/34113547/166121329-08b6b822-4f5f-422e-86c6-72324df4d5ea.png)


* Go to the database server and edit the bind-address!

`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`

![Screenshot from 2022-04-30 18-16-36](https://user-images.githubusercontent.com/34113547/166121350-48944db5-8adb-42be-ba51-22f38e1e558e.png)

![Screenshot from 2022-04-30 18-17-20](https://user-images.githubusercontent.com/34113547/166121360-3ee5f70f-47c9-4444-b153-1eb1b5a8e46e.png)

Then, restart mysql `sudo systemctl restart mysql`

![Screenshot from 2022-04-30 18-18-03](https://user-images.githubusercontent.com/34113547/166121404-b46e2982-9949-420c-a0fe-946860c1736e.png)


* Go to the webserver and try accessing the database with the previously created user
`mysql -u webaccess -ppassword -h 172.31.27.25`

![Screenshot from 2022-04-30 18-18-28](https://user-images.githubusercontent.com/34113547/166121413-292ad0b3-6186-4e71-a4fc-ac95b36c36c3.png)

* On the database server, grant all permissions on the database **tooling** to the user **webaccess** created earlier

![Screenshot from 2022-04-30 19-13-35](https://user-images.githubusercontent.com/34113547/166121503-afb0d753-91d7-4a1e-add6-c86556f20594.png)

* Update the webserver's configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database `mysql -u webaccess -h 172.31.27.25 -ppassword < tooling-db.sql`

`mysql -u webaccess -h 172.31.27.25 -ppassword tooling`

![Screenshot from 2022-04-30 19-15-48](https://user-images.githubusercontent.com/34113547/166121511-a12fbde8-bf13-48da-aaf5-d625f4991eaf.png)


![Screenshot from 2022-04-30 19-21-52](https://user-images.githubusercontent.com/34113547/166121606-e8cf6f24-aacd-4853-8cce-dad8194934b2.png)

* Edit the functions.php file in the html directory on the webserver
`sudo vi functions.php`

![Screenshot from 2022-04-30 19-24-21](https://user-images.githubusercontent.com/34113547/166121613-984fd773-cdb8-427d-bc0a-cfe45f3ed864.png)

* Disable SELinux `sudo setenforce 0`
To make this change permanent run `sudo vi /etc/sysconfig/selinux` and set SELINUX=disabledthen restart httpd

![Screenshot from 2022-04-30 19-28-05](https://user-images.githubusercontent.com/34113547/166121692-b545c3e4-7ace-47f4-bf84-41ca858c3767.png)

* Open your browser and navigate to your webserver's public IP address
`http://18.208.193.12/login.php`

![Screenshot from 2022-04-30 19-28-44](https://user-images.githubusercontent.com/34113547/166121704-d38802c2-0f11-4878-a7e0-431a7cb41334.png)

![Screenshot from 2022-04-30 19-30-07](https://user-images.githubusercontent.com/34113547/166121745-620e772e-f769-4789-94ba-1db3b409ddf9.png)

![Screenshot from 2022-04-30 19-30-18](https://user-images.githubusercontent.com/34113547/166121754-538cedce-8682-4e38-a8ef-fde4f12d6c2c.png)



