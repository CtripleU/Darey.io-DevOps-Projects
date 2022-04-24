# LEMP Stack Implementation

LEMP Stack stands for 
L - Linux E - Nginx M - MySQL P - PHP/Python/Perl <br/>


### STEP 1: Install the Nginx Web server

Run `sudo apt update` to update your instance's package index

You can now install Nginx by running `sudo apt install nginx`

To verify that the installation was successful, run `sudo systemctl status nginx`. 



Navigate to Security Groups on your AWS dashboard and create an inbound rule to open TCP port 80 to enable access to the server on the internet using a web browser.

**Curl** can be used request NGINX on port 80 using the DNS name by running `curl http://127.0.0.1:80` or `curl http:/localhost:80`


You can also navigate to https://<your server's public address>:80


Nginx is fully installed and accessible through the server firewall.

NOTE - A quick way to check your server's public IP address without going to the AWS web console is by running `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`.


### STEP 2: Install MYSQL

Run `sudo apt install mysql-server`

Run `sudo mysql_secure_installation` to validate password login and set up MySQL root user password

MySQL server is now installed and secured. Next, install PHP, which is the final component in the LEMP stack.


### STEP 3: Install PHP

Run `sudo apt install php-fpm php-mysql`


### STEP 4: CONFIGURE NGINX TO USE PHP PROCESSOR

