# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

## Task
This project consists of two parts:

Configure Nginx as a Load Balancer
Register a new domain name and configure secured connection using SSL/TLS certificates

![2](https://user-images.githubusercontent.com/34113547/166161257-cb9bc9c3-cf12-41b7-9eec-fc87d0730d20.png)


### CONFIGURE NGINX AS A LOAD BALANCER

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS 

![Screenshot from 2022-05-01 21-28-43](https://user-images.githubusercontent.com/34113547/166165748-b7f6355f-7be5-4efc-afe2-61b6bed85053.png)

Open TCP port 80 for HTTP connections and TCP port 443 for secured HTTPS connections

![Screenshot from 2022-05-01 23-44-49](https://user-images.githubusercontent.com/34113547/166165797-9bad69e8-44b0-4274-8fb2-910d3606891a.png)

2.Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

`sudo vi /etc/hosts`

![Screenshot from 2022-05-01 21-34-05](https://user-images.githubusercontent.com/34113547/166165821-6ab45405-3ddc-4057-9372-dd1fbdf7b95f.png)


3. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers
`sudo apt install nginx`

![Screenshot from 2022-05-01 23-46-48](https://user-images.githubusercontent.com/34113547/166165854-3535a8c0-402f-4089-b71e-590d0a822671.png)


4. Open the default nginx configuration file and configure the load balancer using Web Servers’ names defined in /etc/hosts

`sudo vi /etc/nginx/nginx.conf`

![Screenshot from 2022-05-01 21-46-51](https://user-images.githubusercontent.com/34113547/166165862-5c39bdfc-85b9-4679-b068-ca3010f488f1.png)

![Screenshot from 2022-05-01 21-45-23](https://user-images.githubusercontent.com/34113547/166165870-174f0bf5-631d-449c-ad11-52cd0dde28df.png)


### REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

1. Register a new domain name with any registrar of your choice in any domain zone

![Screenshot from 2022-05-01 22-33-04](https://user-images.githubusercontent.com/34113547/166165898-eb5aef51-40ce-4c9b-a48c-adec971daa58.png)

![Screenshot from 2022-05-01 22-38-24](https://user-images.githubusercontent.com/34113547/166165908-5f1dddfe-1dd1-4398-887d-ededa2fb5af2.png)


2. Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

![Screenshot from 2022-05-01 22-56-19](https://user-images.githubusercontent.com/34113547/166165941-f5b0735a-2ad5-4dc8-ab83-380d3736d84f.png)

![Screenshot from 2022-05-01 22-56-31](https://user-images.githubusercontent.com/34113547/166165945-436eeda3-54e6-4784-94aa-78b57dd67de0.png)


3. Update A record in your registrar to point to Nginx LB using Elastic IP address

![Screenshot from 2022-05-01 22-58-56](https://user-images.githubusercontent.com/34113547/166165952-d0138d4f-cf98-470d-9787-4da93e0003c4.png)

![Screenshot from 2022-05-01 23-50-26](https://user-images.githubusercontent.com/34113547/166165970-1dcfc4f7-d780-4fd8-a0d4-3dfbd52a32ee.png)


Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol – http://www.toolingproject.tk/

![Screenshot from 2022-05-01 23-27-25](https://user-images.githubusercontent.com/34113547/166165988-b187bfe3-2771-4a0a-9229-1a102d5c6e7d.png)


4.  Configure Nginx to recognize your new domain name by changing `server_name www.domain.com` to `server_name www.toolingproject.tk`


5. Install certbot and request for an SSL/TLS certificate

`sudo systemctl status snapd`

![Screenshot from 2022-05-01 23-07-57](https://user-images.githubusercontent.com/34113547/166166057-8500433e-1ea3-4d1b-bfc3-a015ac39bd0e.png)

`sudo snap install --classic certbot`

![Screenshot from 2022-05-01 23-08-04](https://user-images.githubusercontent.com/34113547/166166059-13ba9dbf-4080-4ed4-86eb-b40947bd0f81.png)

`sudo ln -s /snap/bin/certbot /usr/bin/certbot`

![Screenshot from 2022-05-01 23-39-52](https://user-images.githubusercontent.com/34113547/166166071-7e1e322b-b255-41a0-a75c-56799ca32808.png)

`sudo certbot --nginx`

![Screenshot from 2022-05-01 23-27-07](https://user-images.githubusercontent.com/34113547/166166080-574c87be-749e-4cc8-90f9-e54b5f09f57e.png)

Test secured access to your Web Solution by trying to reach https://www.toolingproject.tk/

![Screenshot from 2022-05-01 23-28-59](https://user-images.githubusercontent.com/34113547/166166096-5bcf2fe4-311d-4dd1-92cc-ff22311e7a4b.png)

![Screenshot from 2022-05-01 23-29-14](https://user-images.githubusercontent.com/34113547/166166106-9b3014b3-1e07-46df-beac-7364586b8bef.png)

![Screenshot from 2022-05-01 23-29-44](https://user-images.githubusercontent.com/34113547/166166109-e4646ab1-e15c-47a6-b7fe-ac0ca5462aa4.png)

![Screenshot from 2022-05-01 23-30-08](https://user-images.githubusercontent.com/34113547/166166115-a7817ee4-7219-478e-8f72-354138101cb5.png)

6. Set up periodical renewal of your SSL/TLS certificate

`sudo certbot renew --dry-run`

![Screenshot from 2022-05-01 23-31-11](https://user-images.githubusercontent.com/34113547/166166121-3be0374f-f6e1-48e8-b5c9-22634077b674.png)

`crontab -e`

![Screenshot from 2022-05-01 23-32-40](https://user-images.githubusercontent.com/34113547/166166130-d8a1aecc-5a7e-44b7-b438-1e8a40c7fd6c.png)










