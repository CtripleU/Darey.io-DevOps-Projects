# MEAN Stack Implementation 

M - MongoDB E - ExpressJS A - AngularJS N - Node.js

### Step 1: Install NodeJs

`sudo apt update`

`sudo apt upgrade`

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`

![Screenshot from 2022-04-27 03-18-17](https://user-images.githubusercontent.com/34113547/165423445-a591f228-4882-46c2-ad18-a9fc98b8ab40.png)

`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

![Screenshot from 2022-04-27 03-19-01](https://user-images.githubusercontent.com/34113547/165423471-b64c2f46-7ebd-4c81-99a5-f74b6564efbc.png)

`sudo apt install -y nodejs`

![Screenshot from 2022-04-27 03-19-21](https://user-images.githubusercontent.com/34113547/165423514-8a2b307b-f230-4d86-b658-e794d3d700d3.png)


### Step 2: Install MongoDB

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

![Screenshot from 2022-04-27 03-20-08](https://user-images.githubusercontent.com/34113547/165423577-1bfc8382-5d28-4dfb-83a2-cf3fd17996c5.png)

`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

![Screenshot from 2022-04-27 03-21-26](https://user-images.githubusercontent.com/34113547/165423622-2b226363-6451-42c7-9315-b34a3815c211.png)

You can now install MongoDB by running `sudo apt install -y mongodb`

![Screenshot from 2022-04-27 03-22-07](https://user-images.githubusercontent.com/34113547/165423672-ca5c31ee-4058-4043-bc74-a8a6b937b745.png)

To start the MongoDB server, run `sudo service mongodb start`

![Screenshot from 2022-04-27 03-23-09](https://user-images.githubusercontent.com/34113547/165423703-83b7df4a-e1bb-4bbf-ac98-a7ee871dddf0.png)

You can verify that the MongoDB service is up and running - `sudo systemctl status mongodb`

![Screenshot from 2022-04-27 03-23-56](https://user-images.githubusercontent.com/34113547/165423727-871a8d90-ed36-486a-a346-adff6211c890.png)

Install NPM - `sudo apt install -y npm`

![Screenshot from 2022-04-27 04-06-47](https://user-images.githubusercontent.com/34113547/165423926-514493e7-0abc-406f-9c8e-d0acfbc16068.png)

I resolved this issue by running

`sudo apt install aptitude`

![Screenshot from 2022-04-27 03-28-01](https://user-images.githubusercontent.com/34113547/165423998-b6a5aa41-a1cf-4b4b-96a4-9f63079c0f02.png)

`sudo aptitude install npm`

![Screenshot from 2022-04-27 03-28-17](https://user-images.githubusercontent.com/34113547/165424018-8f3d5f26-6898-4f40-a6ed-f75f9915ac17.png)

![Screenshot from 2022-04-27 03-29-23](https://user-images.githubusercontent.com/34113547/165424049-98a6abd3-97ea-4b12-b558-636a7bee1783.png)

Install body-parser package to process JSON files passed in requests to the server `sudo npm install body-parser`

![Screenshot from 2022-04-27 03-30-22](https://user-images.githubusercontent.com/34113547/165424073-942e4f5e-3b7f-4d63-8a08-d460cfee23c5.png)

Create a directory named **books** for the project and cd into it. Then initialize npm project

`mkdir books && cd books`

`npm init`

![Screenshot from 2022-04-27 03-33-04](https://user-images.githubusercontent.com/34113547/165424136-9ee18001-4c91-484a-aae9-b0cbd00f43ab.png)

`touch server.js`

![Screenshot from 2022-04-27 03-34-06](https://user-images.githubusercontent.com/34113547/165424243-cac1194e-2679-4984-8c8b-9d2a432a6db2.png)

`vi server.js` to edit the file

![Screenshot from 2022-04-27 03-34-35](https://user-images.githubusercontent.com/34113547/165424382-ee7c26bf-902f-4608-ad73-ae1e0ff64ea6.png)


### Step 3: Install Express and set up routes to the server

`sudo npm install express mongoose`

![Screenshot from 2022-04-27 03-39-53](https://user-images.githubusercontent.com/34113547/165424905-47456073-2524-4d4b-b6ce-24664da4b498.png)

`mkdir apps && cd apps`

![Screenshot from 2022-04-27 03-41-14](https://user-images.githubusercontent.com/34113547/165424949-fa64c14b-7443-4e1a-a074-caebcd144863.png)

`touch routes.js`

![Screenshot from 2022-04-27 03-41-31](https://user-images.githubusercontent.com/34113547/165424978-5a5e8c5f-6e5e-43d3-8602-1bbb6d9590be.png)

`vi routes.js`

![Screenshot from 2022-04-27 03-42-01](https://user-images.githubusercontent.com/34113547/165425009-ad9c6c79-ed3d-4007-9be0-bc405cfcc7b5.png)

`mkdir models && cd models`

![Screenshot from 2022-04-27 03-43-16](https://user-images.githubusercontent.com/34113547/165425037-73672382-7d0a-49be-8586-bae21a40d124.png)

`vi books.js`

![Screenshot from 2022-04-27 03-43-56](https://user-images.githubusercontent.com/34113547/165425056-5da70cae-6d9e-4787-805e-ee7cd5a4e874.png)


### Step 4: Access the routes with AngularJS

Navigate back to the books directory - `cd ../..`

`mkdir public && cd public`

![Screenshot from 2022-04-27 03-46-50](https://user-images.githubusercontent.com/34113547/165425090-15898d20-c79a-45b0-8e71-b96da3e34b60.png)

`vi script.js`

![Screenshot from 2022-04-27 03-47-23](https://user-images.githubusercontent.com/34113547/165425236-f5334428-62bd-483e-9187-285e3dbc2a6b.png)

`vi index.html`

![Screenshot from 2022-04-27 03-48-36](https://user-images.githubusercontent.com/34113547/165425242-08dfa274-95e1-4ad4-a655-3642eab36adc.png)

Navigate back to the **books** directory - `cd ..`

Run `node server.js` to start the node server

![Screenshot from 2022-04-27 03-56-44](https://user-images.githubusercontent.com/34113547/165425362-1a048fa8-7843-4475-b827-6e3a461844e1.png)

I resolved this issue by correcting the routes.js file

![Screenshot from 2022-04-27 03-57-28](https://user-images.githubusercontent.com/34113547/165425488-08cce89d-48f6-4ed1-9712-c44df7db4c4b.png)

Run `node server.js` again

![Screenshot from 2022-04-27 03-55-34](https://user-images.githubusercontent.com/34113547/165425531-61373dd1-abc0-470b-b32d-846170bc5d86.png)

Open http://44.201.2.10:3300/ on a web browser

![Screenshot from 2022-04-27 03-58-26](https://user-images.githubusercontent.com/34113547/165425659-03d28ffe-9a30-45b0-9646-6c320c44e6ff.png)

Note: Make sure TCP port 3300 is open on the AWS Web Console for the ec2 Instance.



