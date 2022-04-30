# WEB SOLUTION WITH WORDPRESS

## Three-tier Architecture
Web and mobile solutions are implemented based on what is called the Three-tier Architecture.
Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers -
* Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
* Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
* Data Access or Management Layer (DAL): This layer is for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

### Step 1: Prepare a Web Server

Launch an EC2 instance that **webserver** and 


Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB


Attach all three volumes to the Web Server EC2 instance


Confirm that they have been attached
`lsblk`


Run `df -h` to see all mounts and free space on the server


Create a single partition on each of the 3 disks using gdisk

`sudo gdisk /dev/xvdf`
`sudo gdisk /dev/xvdg`
`sudo gdisk /dev/xvdh`


View the newly configured partition on each of the 3 disks
`lsblk`


Install lvm2 package
`sudo yum install lvm2`


Check for available partitions
`sudo lvmdiskscan`

Mark each of 3 disks as physical volumes (PVs) to be used by LVM using pvcreate

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

Verify that the Physical volume has been created successfully
`sudo pvs`


Add all 3 PVs to a volume group (VG) with the name **webdata-vg**
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

Run `sudo vgs` to check if the VG was created successfully


Create 2 logical volumes. apps-lv (half of the PV size to store data), and logs-lv (remaining space of the PV size to store data for logs)
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
Verify that the volumes were created successfully 

`sudo lvs`

Check the whole setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

Format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

Create a directory to store website files
`sudo mkdir -p /var/www/html`


Create a directory to store backup of log data
`sudo mkdir -p /home/recovery/logs`

Mount /var/www/html on the apps-lv logical volume
`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

Backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
`sudo rsync -av /var/log/. /home/recovery/logs/`

Mount /var/log on logs-lv logical volume
`sudo mount /dev/webdata-vg/logs-lv /var/log`

Restore log files back into /var/log directory
`sudo rsync -av /home/recovery/logs/. /var/log`

To make the mount configuration persist after restart of the server, you have to udate the /etc/fstab file
The UUID is needed to update the /etc/fstab file. To get the UUID, run `sudo blkid`

Next, update /etc/fstab file using the UUID
`sudo vi /etc/fstab`

Now, test the configuration and reload the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

Run `df -h` to verify the setup



### Step 2: Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.


### Step 3: Install WordPress on your Web Server EC2
First, update the repository
`sudo yum -y update`

Next, Install wget, Apache and other dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

Now, start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```

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


### Step 4: Install MySQL on your DB Server EC2

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
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

### Step 6: Configure WordPress to connect to remote database.
Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

Change permissions and configuration so Apache could use WordPress:

Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)

Try to access from your browser the link to your WordPress http://<Web-Server-Public-IP-Address>/wordpress/
