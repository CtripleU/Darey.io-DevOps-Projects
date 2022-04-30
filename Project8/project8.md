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

* Create an Ubuntu Server 20.04 EC2 instance

* Open TCP port 80 on the server by creating an Inbound Rule in Security Group

* Install Apache Load Balancer on **load-balancer** server and configure it to point traffic coming to LB to both Web Servers

