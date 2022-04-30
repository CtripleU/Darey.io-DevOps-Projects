# WEB SOLUTION WITH WORDPRESS

## Three-tier Architecture
Web and mobile solutions are implemented based on what is called the Three-tier Architecture.
Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers -
* Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
* Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
* Data Access or Management Layer (DAL): This layer is for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

### Step 1: Prepare a Web Server

Launch an EC2 instance that **webserver** 
![Screenshot from 2022-04-29 14-53-28](https://user-images.githubusercontent.com/34113547/166095742-601659fb-d166-495a-8022-06488fd3b256.png)


Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB

![Screenshot from 2022-04-29 15-03-36](https://user-images.githubusercontent.com/34113547/166095751-f4283550-33bf-45d8-9e62-e7ce6fb31f4d.png)

Attach all three volumes to the Web Server EC2 instance

![Screenshot from 2022-04-29 15-05-21](https://user-images.githubusercontent.com/34113547/166095758-b039362b-7934-4164-a101-74d2a770ec5a.png)

Confirm that they have been attached
`lsblk`

![Screenshot from 2022-04-29 15-18-10](https://user-images.githubusercontent.com/34113547/166095763-a65f37c7-3764-4587-8fb9-df6d779eec42.png)

Run `df -h` to see all mounts and free space on the server
![Screenshot from 2022-04-29 15-22-43](https://user-images.githubusercontent.com/34113547/166095783-08f34a63-3c2a-43e1-8c8c-8b7a9a149968.png)


Create a single partition on each of the 3 disks using gdisk

`sudo gdisk /dev/xvdf`
`sudo gdisk /dev/xvdg`
`sudo gdisk /dev/xvdh`


View the newly configured partition on each of the 3 disks
`lsblk`

![Screenshot from 2022-04-29 16-36-18](https://user-images.githubusercontent.com/34113547/166095834-77b6a4f6-9253-4ac3-99c5-bd79f389317f.png)


Install lvm2 package
`sudo yum install lvm2`

![Screenshot from 2022-04-29 16-43-43](https://user-images.githubusercontent.com/34113547/166095839-99fb5935-26e9-471b-80d6-c02c47464f98.png)

Check for available partitions
`sudo lvmdiskscan`

![Screenshot from 2022-04-29 16-44-08](https://user-images.githubusercontent.com/34113547/166095855-1788a3ee-a50c-49ab-af22-19967c72582a.png)

Mark each of 3 disks as physical volumes (PVs) to be used by LVM using pvcreate

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```
![Screenshot from 2022-04-29 16-46-47](https://user-images.githubusercontent.com/34113547/166095862-ed0ad6bd-1d53-47d4-a9bf-2ff1a2c892d8.png)

Verify that the Physical volume has been created successfully
`sudo pvs`

![Screenshot from 2022-04-29 16-47-49](https://user-images.githubusercontent.com/34113547/166095867-ececcaf1-1d10-4385-92ca-ecc304c0b29c.png)

Add all 3 PVs to a volume group (VG) with the name **webdata-vg**
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

![Screenshot from 2022-04-29 16-50-00](https://user-images.githubusercontent.com/34113547/166095874-01f8195d-2c5b-4fad-85fb-d02f049bc7e6.png)

Run `sudo vgs` to check if the VG was created successfully

![Screenshot from 2022-04-29 16-50-29](https://user-images.githubusercontent.com/34113547/166095879-75774c51-86b6-48f6-a2f6-25acef3fa456.png)

Create 2 logical volumes. apps-lv (half of the PV size to store data), and logs-lv (remaining space of the PV size to store data for logs)
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
![Screenshot from 2022-04-29 16-54-57](https://user-images.githubusercontent.com/34113547/166095885-a630e9f9-9788-4d81-a4f1-5504f21e4f00.png)


Verify that the volumes were created successfully 

`sudo lvs`

![Screenshot from 2022-04-29 16-55-13](https://user-images.githubusercontent.com/34113547/166095888-c869661f-0d97-4012-a211-83ecbd5e6fd5.png)


Check the whole setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```
![Screenshot from 2022-04-29 16-56-40](https://user-images.githubusercontent.com/34113547/166095892-17f68a3c-7797-4a85-aec8-34dae84d93c0.png)

Format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
![Screenshot from 2022-04-29 17-02-34](https://user-images.githubusercontent.com/34113547/166095908-99e3b3ef-5408-455c-9291-06152edf3bb6.png)

Create a directory to store website files
`sudo mkdir -p /var/www/html`

Create a directory to store backup of log data
`sudo mkdir -p /home/recovery/logs`

![Screenshot from 2022-04-29 17-04-13](https://user-images.githubusercontent.com/34113547/166095933-2d2c05c9-185e-4192-b180-dfda07633271.png)

Mount /var/www/html on the apps-lv logical volume
`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

![Screenshot from 2022-04-29 17-06-38](https://user-images.githubusercontent.com/34113547/166095942-73771f1b-5955-4987-90b0-252d9f8fa61a.png)

Backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
`sudo rsync -av /var/log/. /home/recovery/logs/`

![Screenshot from 2022-04-29 17-06-33](https://user-images.githubusercontent.com/34113547/166095945-ed6c1cec-5454-4610-95e9-00ea57c49abb.png)

Mount /var/log on logs-lv logical volume
`sudo mount /dev/webdata-vg/logs-lv /var/log`

![Screenshot from 2022-04-29 17-07-41](https://user-images.githubusercontent.com/34113547/166095980-06126c7b-1c51-4d47-a3f1-cac9d5d9a8c9.png)

Restore log files back into /var/log directory
`sudo rsync -av /home/recovery/logs/. /var/log`

![Screenshot from 2022-04-29 17-08-30](https://user-images.githubusercontent.com/34113547/166095985-e0292f43-16ac-4e45-8091-3b4ba933a248.png)

To make the mount configuration persist after restart of the server, you have to update the /etc/fstab file
The UUID is needed to update the /etc/fstab file. To get the UUID, run `sudo blkid`

![Screenshot from 2022-04-29 23-31-35](https://user-images.githubusercontent.com/34113547/166096013-24c19b2d-05ae-4411-9ff4-b12df91b2442.png)

Next, update /etc/fstab file using the UUID
`sudo vi /etc/fstab`
![Screenshot from 2022-04-29 23-40-34](https://user-images.githubusercontent.com/34113547/166096009-b6117aa9-19ff-41f7-a47d-67c252389b6e.png)

Now, test the configuration and reload the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```
![Screenshot from 2022-04-29 23-43-06](https://user-images.githubusercontent.com/34113547/166096017-cc7aa9fe-0a26-4861-8ba0-1ec1e75e96ed.png)

Run `df -h` to verify the setup

![Screenshot from 2022-04-29 23-44-28](https://user-images.githubusercontent.com/34113547/166096021-aa9b11eb-9c0d-4374-8e48-56dd35b32084.png)


### Step 2: Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

![Screenshot from 2022-04-30 00-21-06](https://user-images.githubusercontent.com/34113547/166096044-648b2676-153e-4179-b935-32038783370d.png)


### Step 3: Install WordPress on your Web Server EC2
First, update the repository
`sudo yum -y update`

Next, Install wget, Apache and other dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![Screenshot from 2022-04-30 00-23-01](https://user-images.githubusercontent.com/34113547/166096077-42fe099c-650f-435b-87ba-a9420b31a2b3.png)

Now, start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```
![Screenshot from 2022-04-30 00-23-37](https://user-images.githubusercontent.com/34113547/166096085-daeab813-8156-4236-b0a5-dce770614e9c.png)

Install PHP and it’s depemdencies
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1
```

To restart Apache, run `sudo systemctl restart httpd`

Download Wordpress and copy it to var/www/html

```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```

Configure SELinux Policies

```
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```


### Step 4: Install MySQL on theDB Server EC2

```
sudo yum update
sudo yum install mysql-server
```

Run `sudo systemctl status mysqld` to verify if MySQL service is renning.

```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

Step 5: Configure DB to work with WordPress
```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER 'wordpress'@'172.31.25.215' IDENTIFIED BY 'password';
GRANT ALL ON wordpress.* TO 'wordpress'@'172.31.25.215';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

Update the /etc/my.cnf file on the DB server with the bind address

`sudo vi /etc/my.cnf`

![Screenshot from 2022-04-30 02-42-04](https://user-images.githubusercontent.com/34113547/166096714-0ab04783-bb90-4639-b71c-dd10296cc1d1.png)

### Step 6: Configure WordPress to connect to remote database.
Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u user -p -h 172.31.25.228
```

Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

![Screenshot from 2022-04-30 01-09-40](https://user-images.githubusercontent.com/34113547/166096336-7468637c-5c5b-4b7d-8966-237ef4a8a2ea.png)

Change the EC2 permissions and configuration so Apache canuse WordPress

Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)

Update **wp-config.php** with the username, database, and port values so that it can connect to the database server.

![Screenshot from 2022-04-30 03-26-08](https://user-images.githubusercontent.com/34113547/166096407-c0b32319-06c2-4cbb-8c92-8b4b3a89c8ae.png)

Finally, reload httpd and mysqld

```
sudo systemctl restart mysqld
sudo systemctl reload mysqld
```

Open a browser and navigate to the wordpress link - http://http://35.172.215.181/wordpress//wordpress/

![Screenshot from 2022-04-30 09-01-37](https://user-images.githubusercontent.com/34113547/166096780-0f4e945c-1d70-4dba-b791-25f118865987.png)

