# CLIENT-SERVER ARCHITECTURE WITH MYSQL

### STEP 1
Create and configure two Linux-based EC2 instances

You can give the name tags of **mysql-server** and **mysql-client** to each of them respectively.

![Screenshot from 2022-04-29 13-00-54](https://user-images.githubusercontent.com/34113547/165932596-f4ac60a7-c6fd-402a-ad80-328a4438575d.png)

### STEP 2
Run `sudo apt update` and `sudo apt upgrade` on both nstances

### STEP 3
Install MySQL Server software  on **mysql-server**

### STEP 4
Install MySQL Client software on the **mysql-client** instance

### STEP 5
Run `sudo mysql_secure_installation` to secure MySQL server deploymemt using the pre-installed security script

### STEP 6
Ensure that MysQL TCP port 3306 is open in the EC2 Security group Inbound rules for **mysql server**

### STEP 7
Create a remote user
`sudo mysql`

`mysql> CREATE USER 'user'@'%' IDENTIFIED BY 'Pass1234_';`

###  STEP 8
Create a database
`mysql> CREATE DATABASE test;`

### STEP 9
Grant the user prileges to the database and flush privileges
`mysql> GRANT ALL ON test.* TO 'user'@'%' WITH GRANT OPTION;`
`mysql> FLUSH PRIVILEGES;`

### STEP 10
Configure **mysql-server** to allow connections from remote hosts by editing **bind-address**

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

Restart MySQL to apply the changes made to the config file

`sudo systemctl restart mysql`

### STEP 11
Connect remotely to mysql server from **mysql-client** Linux Server Database Engine without using SSH

Run `mysql -u user -h 172.31.29.94 -p` on the **mysql-client** server


From the picture that we can now connect to **mysql-server** and access databases on it from **mysql-client**
