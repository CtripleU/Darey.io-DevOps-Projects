# LOAD BALANCER SOLUTION WITH APACHE

When you have just one Web server and load increases – you want to serve more and more customers, you can add more CPU and RAM or completely replace the server with a more powerful one – this is called "vertical scaling". This approach has limitations – at some point you reach the maximum capacity of CPU and RAM that can be installed into your server.

Another approach used to cater for increased traffic is "horizontal scaling" – distributing load across multiple Web servers. This approach is much more common and can be applied almost seamlessly and almost infinitely (you can imagine how many server Google has to serve billions of search requests).

In our set up in Project-7 we had 3 Web Servers and each of them had its own public IP address and public DNS name. A client has to access them by using different URLs, which is not a nice user experience to remember addresses/names of even 3 server, let alone millions of Google servers.

In order to hide all this complexity and to have a single point of access with a single public IP address/name, a Load Balancer can be used. A Load Balancer (LB) distributes clients’ requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.

In this project we will enhance our Tooling Website solution by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL.

![2](https://user-images.githubusercontent.com/34113547/166122264-b5940bfb-30c7-4e59-84b6-92e3e9827430.png)


## Task
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

### Prerequisites
The following servers installed and configured within Project-7:
* Two RHEL8 Web Servers
* One MySQL DB Server (based on Ubuntu 20.04)
* One RHEL8 NFS server

![2](https://user-images.githubusercontent.com/34113547/166122278-a70040cb-36b8-4873-9a44-2c67ee1329d6.png)



### Configuring Apache as a load Balancer

* Create an Ubuntu Server 20.04 EC2 instance **load-balancer**

![Screenshot from 2022-04-30 22-55-39](https://user-images.githubusercontent.com/34113547/166130984-b1f250fc-71c1-41f5-b652-79fcb1c77339.png)

* Open TCP port 80 on the server by creating an Inbound Rule in Security Group


* Install Apache Load Balancer on **load-balancer** server and configure it to point traffic coming to LB to both Web Servers
```
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

#Restart apache2 service
sudo systemctl restart apache2
```


* Make sure Apache is up and running
 `sudo systemctl status apache2`
 
 ![Screenshot from 2022-05-01 00-29-09](https://user-images.githubusercontent.com/34113547/166131019-78940149-a812-457c-88ff-2d86e6021c61.png)


### Configure load balancing
 
* `sudo vi /etc/apache2/sites-available/000-default.conf`

![Screenshot from 2022-05-01 00-34-53](https://user-images.githubusercontent.com/34113547/166131028-7c278d5d-3725-43da-8b3f-7e82e94a735d.png)
![Screenshot from 2022-05-01 00-37-29](https://user-images.githubusercontent.com/34113547/166131038-90d70896-1df4-4cb0-8f4c-48a78105dabf.png)

Run `sudo systemctl restart apache2` to restart Apache server


* To verify that the configuration works, try to access the load balancer's public IP address from the browser
`http://18.234.174.177/login.php`

![Screenshot from 2022-05-01 00-44-02](https://user-images.githubusercontent.com/34113547/166131045-cc8afc7d-73e2-4a78-b3e1-e27ac03d0c95.png)

* Access the logs by running `sudo tail -f /var/log/httpd/access_log`

![Screenshot from 2022-05-01 01-05-52](https://user-images.githubusercontent.com/34113547/166131052-4be0a5dc-61ea-4322-94b2-d21278453ea4.png)

As shown in the screenshot above, the app served was using a public IP initially and then changed to the private IP of th eload balancer. Whenever the website is refreshed, more entries are aded to the log.
The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be disctributed evenly between them.


### Optional Step: Configure Local DNS Names Resolution

* Open the hosts file on your load balancer server and add two records with the private IP address and arbitrary name for both of your Web Servers
`sudo vi /etc/hosts`

![Screenshot from 2022-05-01 05-08-24](https://user-images.githubusercontent.com/34113547/166131065-ae449917-b927-4795-8ec2-d98ac55f34bb.png)

* Next, update the LB config file with those names instead of IP addresses

`sudo vi /etc/apache2/sites-available/000-default.conf`

![Screenshot from 2022-05-01 05-10-27](https://user-images.githubusercontent.com/34113547/166131071-0be649e9-d5b0-4705-932a-1bcd61b51794.png)

* Now restart Apache and ensure that the website is still reachable

![Screenshot from 2022-05-01 05-14-20](https://user-images.githubusercontent.com/34113547/166131081-a7bbb504-38a0-45c6-9440-9776b7b28fd7.png)



