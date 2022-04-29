# CLIENT-SERVER ARCHITECTURE WITH MYSQL

### STEP 1
Create and configure two Linux-based EC2 instances

You can give the name tags of **mysql-server** and **mysql-client** to each of them respectively.

![Screenshot from 2022-04-29 10-41-22](https://user-images.githubusercontent.com/34113547/165932762-d09c244b-72fc-442c-933f-c1ccf9bae78f.png)

### STEP 2
Run `sudo apt update` and `sudo apt upgrade` on both nstances

### STEP 3
Install MySQL Server software  on **mysql-server**

![Screenshot from 2022-04-29 10-51-45](https://user-images.githubusercontent.com/34113547/165932818-87dadaab-7a12-46e5-878d-54f8abe3de04.png)

### STEP 4
Install MySQL Client software on the **mysql-client** instance

![Screenshot from 2022-04-29 11-00-11](https://user-images.githubusercontent.com/34113547/165932844-d8bf723c-5771-4e9d-aba3-4b2aca7a9650.png)

### STEP 5
Run `sudo mysql_secure_installation` to secure MySQL server deploymemt using the pre-installed security script

![Screenshot from 2022-04-29 12-38-45](https://user-images.githubusercontent.com/34113547/165932897-9af09d0a-e435-4604-b2ea-16165289ebe5.png)

### STEP 6
Ensure that MysQL TCP port 3306 is open in the EC2 Security group Inbound rules for **mysql server**

![Screenshot from 2022-04-29 12-50-37](https://user-images.githubusercontent.com/34113547/165932933-48010dcf-07ce-4e6b-b468-8651a99d5117.png)


### STEP 7
Create a remote user
`sudo mysql`

`mysql> CREATE USER 'user'@'%' IDENTIFIED BY 'Pass1234_';`

![Screenshot from 2022-04-29 12-43-41](https://user-images.githubusercontent.com/34113547/165933013-a5a97eed-f2bb-44ae-811f-e0be0a40689c.png)

###  STEP 8
Create a database
`mysql> CREATE DATABASE test;`

![Screenshot from 2022-04-29 12-47-04](https://user-images.githubusercontent.com/34113547/165933060-ad2ee101-c901-4da4-b5e8-9d8459c23edd.png)

### STEP 9
Grant the user prileges to the database and flush privileges

`mysql> GRANT ALL ON test.* TO 'user'@'%' WITH GRANT OPTION;`

`mysql> FLUSH PRIVILEGES;`

![Screenshot from 2022-04-29 12-48-25](https://user-images.githubusercontent.com/34113547/165933139-1a620887-7e5d-4d62-a7d9-8d7142c287fd.png)


### STEP 10
Configure **mysql-server** to allow connections from remote hosts by editing **bind-address**

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

![Screenshot from 2022-04-29 11-56-51](https://user-images.githubusercontent.com/34113547/165933231-8cdecadc-7aea-47d8-ac7e-fd6e9b942b90.png)

Restart MySQL to apply the changes made to the config file

`sudo systemctl restart mysql`

### STEP 11
Connect remotely to mysql server from **mysql-client** Linux Server Database Engine without using SSH

Run `mysql -u user -h 172.31.29.94 -p` on the **mysql-client** server

![Screenshot from 2022-04-29 12-55-46](https://user-images.githubusercontent.com/34113547/165933356-436d66bd-625e-48dc-8c85-36b363c7d9da.png)

From the picture that we can now connect to **mysql-server** and access databases on it from **mysql-client**
