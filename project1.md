# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

LAMP Stack is a software stack used for building and delivering web applications. 
L - Linux A - Apache M - MySQL P - PHP/Python/Perl <br/>


STEP 0: Prepare the prerequisites

Spin up a new EC2 instance and connect to it using SSH.
![Screenshot from 2022-04-18 15-07-08](https://user-images.githubusercontent.com/34113547/163896922-965a5d2f-e5ff-456d-bf62-73030fb059ca.png)

STEP 1: Install Apache 

Apache needs to be installed using **apt**, which is Ubuntu's package manager.


First, run `sudo apt update` to update the packages available in the package manager.

![Screenshot from 2022-04-18 15-13-54](https://user-images.githubusercontent.com/34113547/163897043-d7ae1381-208f-4d8b-9838-e285293394c9.png)

Next, run `sudo apt install apache2` to install Apache.

![Screenshot from 2022-04-18 15-14-33](https://user-images.githubusercontent.com/34113547/163897084-c570eda2-13d9-4d52-a8fd-270f61d61f63.png)

Ensure Apache is running - `sudo systemctl status apache2`

![Screenshot from 2022-04-18 15-16-11](https://user-images.githubusercontent.com/34113547/163897159-985f957b-47fa-498a-9bc5-0546f769676b.png)

Go to the EC2 instance Security Groups and click **Edit inbound rules**. Add a new rule with details Type - HTTP, Protocol - TCP, and Port range - 80, and Source - Anywhere-IPv4 (this has a CIDR block of 0.0.0.0/0). The reason for this action is to allow inbound connection through port 80, as it is the default port that web browsers use to access web pages on the internet.

![Screenshot from 2022-04-18 15-25-26](https://user-images.githubusercontent.com/34113547/163897229-7e22ca50-b21e-480d-b34b-1febd738ce9d.png)

Open the IP adress:80 on the browser

![Screenshot from 2022-04-18 15-30-04](https://user-images.githubusercontent.com/34113547/163897304-ffee31ed-0e9e-48f2-9f3f-61a393419d7e.png)

STEP 2: Install MySQL

Run `sudo apt install mysql-server`

![Screenshot from 2022-04-18 15-48-22](https://user-images.githubusercontent.com/34113547/163897394-4fce413e-62fb-45b2-aaec-ad66ec1c67ba.png)

Run `sudo mysql_secure_installation1` to secure MySQL server deploymemt using the pre-installed security script.

![Screenshot from 2022-04-18 15-55-16](https://user-images.githubusercontent.com/34113547/163897427-aab944d0-fc31-406a-ac2a-b3e08a313f5c.png)

You can log in to the MySQL console by running `sudo mysql` and exit by simply typing `exit`

![Screenshot from 2022-04-18 15-57-44](https://user-images.githubusercontent.com/34113547/163897470-3f7d5b2a-adfd-481c-a6dd-016d005d1a8e.png)

STEP 3: Install PHP

To install PHP along with php-mysql and libapache2-mod-php, run `sudo apt install php libapache2-mod-php php-mysql`

![Screenshot from 2022-04-18 16-04-25](https://user-images.githubusercontent.com/34113547/163897533-72cdce0c-e00e-47bc-a5e0-3b825e9bb240.png)

You can confirm that PHP is installed and its version by running `php -v`

![Screenshot from 2022-04-18 16-05-52](https://user-images.githubusercontent.com/34113547/163897592-dbe2d8f6-d297-4a47-a1e0-862ffdd93516.png)

Note: LAMP stack is now fully set-up.


STEP 4: Create a virtual host for your website using Apache

The default directory Apache serves documents from is the var/www/html directory. For this project, I will create another directory.
`sudo mkdir /var/www/lampproject`

![Screenshot from 2022-04-18 16-31-17](https://user-images.githubusercontent.com/34113547/163897701-d1020b88-2bb6-4241-bb3e-75e155a7d93a.png)

To assign ownership of the new directory to the current system user, run `sudo chown -R $USER:$USER /var/www/lampproject`

Next, create a new config file in Apache's **sites-available** directory and open it in vim.
`sudo vi /etc/apache2/sites-available/lampproject.conf`
Edit the file and put the configuration in it.

![Screenshot from 2022-04-18 16-52-38](https://user-images.githubusercontent.com/34113547/163897789-6224c279-9b14-4f66-8f6f-5f4d532ecc9c.png)

Run `sudo a2ensite lampproject` to enable new virtual host.
Run `sudo a2dissite 000-default` to disable Apache's default website.
Run `sudo systemctl reload apache2` to reload Apache with the new changes.

![Screenshot from 2022-04-18 17-02-11](https://user-images.githubusercontent.com/34113547/163897919-3f5aa1d9-37e8-41bf-b799-df49cf3d1b83.png)

Run `sudo echo 'Hello LAMP from hostname' $(curl -s http://18.213.110.84/latest/meta-data/public-ipv4) > /var/www/lampproject/index.html` to echo it into a new file named index.html

Open the browser and navigate to the EC2 instance's IP address 
`http://44.204.98.216:80`

![Screenshot from 2022-04-19 02-06-41](https://user-images.githubusercontent.com/34113547/163897942-db994346-758b-479e-9e1a-ab4566b908e6.png)

STEP 5: Enable PHP on the website

Edit the DirectoryIndex directive to enable index.php take precedence over index.html
`sudo vim /etc/apache2/mods-enabled/dir.conf`

![Screenshot from 2022-04-19 02-14-12](https://user-images.githubusercontent.com/34113547/163898011-3fd0f116-bb88-4253-999b-04cdba57ad56.png)

![Screenshot from 2022-04-19 02-16-47](https://user-images.githubusercontent.com/34113547/163898013-02255a9b-4e45-48d3-93f7-2fcc7208936e.png)


Reload Apache by running `sudo systemctl reload apache2`

Run `vim /var/www/lampproject/index.php` and add some PHP code in it

Refresh the browser

![Screenshot from 2022-04-19 02-21-31](https://user-images.githubusercontent.com/34113547/163898053-821d6127-6ecf-4a4d-81c1-8ce015e0bdd3.png)

This page shows a successful PHP installation.
