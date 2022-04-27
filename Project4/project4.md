# MEAN Stack Implementation 

M - MongoDB E - ExpressJS A - AngularJS N - Node.js

### Step 1: Install NodeJs

`sudo apt update`

`sudo apt upgrade`

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`

`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

`sudo apt install -y nodejs`

### Step 2: Install MongoDB

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

You can now install MongoDB by running `sudo apt install -y mongodb`

To start the MongoDB server, run `sudo service mongodb start`

You can verify that the MongoDB service is up and running - `sudo systemctl status mongodb`

Install NPM - `sudo apt install -y npm`


I resolved this issue by running

`sudo apt install aptitude`

`sudo aptitude install npm`


Install body-parser package to process JSON files passed in requests to the server `sudo npm install body-parser`

Create a directory named **books** for the project and cd into it. Then initialize npm project

`mkdir books && cd books`

`npm init`

`touch server.js`

`vi server.js` to edit the file


### Step 3: Install Express and set up routes to the server

`sudo npm install express mongoose`

`mkdir apps && cd apps`

`touch routes.js`

`vi routes.js`

`mkdir models && cd models`

`vi books.js`



### Step 4: Access the routes with AngularJS

Navigate back to the books directory - `cd ../..`

`mkdir public && cd public`

`vi script.js`

`vi index.html`

Navigate back to the **books** directory - `cd ..`

Run `node server.js` to start the node server

Open http://44.201.2.10:3300/ on a web browser

Note: Make sure TCP port 3300 is open on the AWS Web Console for the ec2 Instance.



