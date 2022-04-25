# LEMP Stack Implementation

LEMP Stack stands for 
L - Linux E - Nginx M - MySQL P - PHP/Python/Perl <br/>


### STEP 1: Install the Nginx Web server

Run `sudo apt update` to update your instance's package index

You can now install Nginx by running `sudo apt install nginx`

![Screenshot from 2022-04-24 23-03-10](https://user-images.githubusercontent.com/34113547/165006334-48fb9e61-2e08-4f0e-902d-638cff3444d5.png)

To verify that the installation was successful, run `sudo systemctl status nginx`. 

![Screenshot from 2022-04-25 00-37-40](https://user-images.githubusercontent.com/34113547/165006498-7736a666-98d4-4d92-9f9a-303653c1d073.png)

Navigate to Security Groups on your AWS dashboard and create an inbound rule to open TCP port 80 to enable access to the server on the internet using a web browser.

**Curl** can be used request NGINX on port 80 using the DNS name by running `curl http://127.0.0.1:80` or `curl http:/localhost:80`

![Screenshot from 2022-04-25 01-03-33](https://user-images.githubusercontent.com/34113547/165006524-df1bdafd-ef9b-42b0-87e1-cc223466526e.png)

You can also navigate to https://<your server's public address>:80

![Screenshot from 2022-04-25 01-04-23](https://user-images.githubusercontent.com/34113547/165006574-5f90c01a-39e4-4e94-a225-db005ee4bf19.png)

Nginx is fully installed and accessible through the server firewall.

NOTE - A quick way to check your server's public IP address without going to the AWS web console is by running <br>
`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`.


### STEP 2: Install MYSQL

Run `sudo apt install mysql-server`

![Screenshot from 2022-04-25 01-16-08](https://user-images.githubusercontent.com/34113547/165006604-f116eff0-7420-4d76-a1d0-735c14432a63.png)

Run `sudo mysql_secure_installation` to validate password login and set up MySQL root user password

![Screenshot from 2022-04-25 01-18-46](https://user-images.githubusercontent.com/34113547/165006624-db97676b-c47a-470e-b6f8-81b2680fc826.png)

MySQL server is now installed and secured. Next, install PHP, which is the final component in the LEMP stack.


### STEP 3: Install PHP

Run `sudo apt install php-fpm php-mysql`

![Screenshot from 2022-04-25 01-25-03](https://user-images.githubusercontent.com/34113547/165006649-e98c26e8-ed8b-470f-8a77-a6d62e758439.png)

### STEP 4: CONFIGURE NGINX TO USE PHP PROCESSOR

When using the Nginx web server, it is possible to use server blocks, similar to virtual hosts in Apache, to encapsulate configuration details and host more than one domain on a single server. 


To create a root directory for the example domain name, run `sudo mkdir /var/www/LEMPproject`

![Screenshot from 2022-04-25 01-33-15](https://user-images.githubusercontent.com/34113547/165006685-c2c52919-d19a-48e4-a5c2-928e450d5be1.png)

Next, assign ownership of the directory to the current user using the $USER variable <br>
`sudo chown -R $USER:$USER /var/www/LEMPproject`

![Screenshot from 2022-04-25 01-36-49](https://user-images.githubusercontent.com/34113547/165006717-25950836-cbc6-47cd-a092-4e7a7552b179.png)

Open a new config file in Nginx’s sites-available directory <br>
`sudo nano /etc/nginx/sites-available/LEMPproject`

![Screenshot from 2022-04-25 01-40-27](https://user-images.githubusercontent.com/34113547/165006728-33d8e3a8-b629-4581-97d4-f575093ac5fb.png)

To activate the new configuration, link the config file from Nginx’s sites-enabled directory by running <br>
`sudo ln -s /etc/nginx/sites-available/LEMPproject /etc/nginx/sites-enabled/`

![Screenshot from 2022-04-25 01-43-10](https://user-images.githubusercontent.com/34113547/165006753-a9a629b1-3ccc-4ce5-9c8c-7bf5f568d5b7.png)

Run `sudo nginx -t` to test for syntax errors in the configuration

![Screenshot from 2022-04-25 01-44-01](https://user-images.githubusercontent.com/34113547/165006806-6c2460a1-e4a4-4ae7-8cde-667c242b3277.png)

To disable the default Nginx host that is currently configured to listen on port 80, run `sudo unlink /etc/nginx/sites-enabled/default` <br>
Then reload Nginx to apply the changes `sudo systemctl reload nginx`

![Screenshot from 2022-04-25 01-46-05](https://user-images.githubusercontent.com/34113547/165006844-28963d90-171e-4596-8a2d-d4875927f1c6.png)

Create an index.html file in the web root /var/www/LEMPproject to test that the new server block works as expected <br>
`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/LEMPproject/index.html`

![Screenshot from 2022-04-25 01-48-41](https://user-images.githubusercontent.com/34113547/165006925-47deefd1-0731-4693-9973-5ad1b62bb250.png)

Open https://<your server's public address>:80 on your browser

![Screenshot from 2022-04-25 01-50-13](https://user-images.githubusercontent.com/34113547/165006964-294c1d39-e6c9-42d6-90de-bda28b0bea68.png)

Nginx is running as expected.

### STEP 5: TEST PHP WITH NGINX

Create a test PHP file in the document root to validate that Nginx can correctly hand .php files off to your PHP processor <br>
`sudo nano /var/www/LEMPproject/info.php`

![Screenshot from 2022-04-25 02-18-41](https://user-images.githubusercontent.com/34113547/165006988-f946cff7-34ec-40d3-9b5d-bfdb08317ae3.png)

Open https://<your server's public address>/info.php on your browser

![Screenshot from 2022-04-25 02-38-40](https://user-images.githubusercontent.com/34113547/165007092-8eddb7ef-e6e4-43d1-9ee8-4c37bfed119b.png)

`sudo rm /var/www/your_domain/info.php`

### Step 6: Retrieve data from MySQL database with PHP

Create a database named example_database and a user named example_user, but you can replace these names with different values.

Run `sudo mysql`

From your MySQL console, run `mysql> CREATE DATABASE 'example_database';`

![Screenshot from 2022-04-25 02-59-41](https://user-images.githubusercontent.com/34113547/165007138-4266c42d-f905-4d1d-b9b9-bff7e65fdd1f.png)

Now you can create a new user and grant the user full privileges on the database you have just created.

Create a new user named example_user, using mysql_native_password as default authentication method <br>
`mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

![Screenshot from 2022-04-25 03-42-02](https://user-images.githubusercontent.com/34113547/165007404-a00bd084-46e9-42a0-86c1-cceac55b3f04.png)

Next, give this user permission over the example_database database <br>
`mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

Now exit the MySQL shell with `mysql> exit`

Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials <br>
`mysql -u example_user -p`

To log in, you will need to use the password used when creating the example_user user 

Run `mysql> SHOW DATABASES;` to heck that you have access to the database

![Screenshot from 2022-04-25 03-15-55](https://user-images.githubusercontent.com/34113547/165007946-86f2387d-db5e-48c8-b80f-e54606287052.png)

Create a test table named todo_list form the MySQL console

![Screenshot from 2022-04-25 03-17-18](https://user-images.githubusercontent.com/34113547/165008077-54c84997-e489-4091-b2f0-5ba04ae52dcc.png)

Insert some rows of content in the test table

![Screenshot from 2022-04-25 03-18-52](https://user-images.githubusercontent.com/34113547/165008119-03512908-f5f6-43b4-b8ad-3b4298ce9062.png)

Run `mysql>  SELECT * FROM example_database.todo_list;` to confirm that the data was successfully saved to the table

![Screenshot from 2022-04-25 03-19-54](https://user-images.githubusercontent.com/34113547/165008150-ef1ff3ab-144f-44aa-a475-d3b27f8de18e.png)

Exit the MySQL console

Create a PHP script that will connect to MySQL and query for your content. To do this, create a new PHP file in your custom web root directory <br>
`nano /var/www/LEMPproject/todo_list.php`

![Screenshot from 2022-04-25 03-22-55](https://user-images.githubusercontent.com/34113547/165008177-263cf4a7-c7ea-405a-948c-8b597c2129ab.png)

![Screenshot from 2022-04-25 03-23-06](https://user-images.githubusercontent.com/34113547/165008228-f766c9c9-e524-473e-a765-73cfa7945e20.png)

Open https://<your server's public address>/todo_list.php on your browser

![Screenshot from 2022-04-25 03-24-20](https://user-images.githubusercontent.com/34113547/165008260-0e24c054-4cea-4c0c-ae08-11b19b38fca7.png)

The PHP environment is connected and is interacting with the MySQL server.
