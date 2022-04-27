# MERN Stack Implementation - SIMPLE TO-DO APPLICATION ON MERN WEB STACK


### STEP 1: Backend Configuration

First, update and upgrade Ubuntu by running `sudo apt update` and then `sudo apt upgrade`.

![Screenshot from 2022-04-26 20-42-29](https://user-images.githubusercontent.com/34113547/165414101-7e8fb154-a9f7-4538-bb07-bf051a45b5ea.png)
![Screenshot from 2022-04-26 20-42-57](https://user-images.githubusercontent.com/34113547/165414118-837ae3e8-1c98-4fcc-9f21-450f4f1c23c3.png)

Next, get the location of Node.js from Ubuntu repositories by running `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

![Screenshot from 2022-04-26 20-45-51](https://user-images.githubusercontent.com/34113547/165414402-d57712f3-46c4-4c26-a2c8-f6ec257ea48a.png)

You can now install Node.js and NPM. Run `sudo apt-get install -y nodejs`

![Screenshot from 2022-04-26 20-46-24](https://user-images.githubusercontent.com/34113547/165414432-de5c4462-0148-48c3-9832-c6c443175e2e.png)

Create a new directory for the project named **Todo** and navigate into the directory.

![Screenshot from 2022-04-26 20-50-17](https://user-images.githubusercontent.com/34113547/165414471-4741d796-2468-4c76-be7f-e36c4c6e0737.png)

Initialize the project - `npm init`

![Screenshot from 2022-04-26 20-53-48](https://user-images.githubusercontent.com/34113547/165414495-e18e6cfa-d327-44ff-bfeb-d8b6e2bc7a42.png)

Install Express - `npm install express`

![Screenshot from 2022-04-26 20-55-45](https://user-images.githubusercontent.com/34113547/165414521-17c7874d-ea36-44f2-89e7-95401ba46425.png)

Create an index.js file - `touch index.js`

![Screenshot from 2022-04-26 20-56-56](https://user-images.githubusercontent.com/34113547/165414545-7e24f1aa-f5b1-4308-b7d3-be2e558bc787.png)

Install the Dotenv module - `npm install dotenv`

![Screenshot from 2022-04-26 20-58-07](https://user-images.githubusercontent.com/34113547/165414573-001cde97-a0ee-410b-91d1-d4f7bc2c921b.png)

Edit the index.js file in Vim

![Screenshot from 2022-04-26 20-59-51](https://user-images.githubusercontent.com/34113547/165414635-02450b1a-c5d1-4ed3-b6bb-5044a2f351f9.png)

Start the server by running `node index.js`

![Screenshot from 2022-04-26 21-01-11](https://user-images.githubusercontent.com/34113547/165414659-ed9a0472-9541-4ec9-ada2-d9fa379ba132.png)

Go to Security Groups for your ec2 instance and create an inbound rule to open TCP port 5000

Try to access your server’s Public IP address followed by port 5000

![Screenshot from 2022-04-26 21-06-59](https://user-images.githubusercontent.com/34113547/165414695-cacad815-4428-4e2e-aba9-dca33b41902b.png)

#### Routes

The To-Do application needs to be able to create a new task, display list of all tasks, and delete a completed task.
Create - POST
Display - GET
Delete - DELETE

Now, we will create routes that will define various endpoints that the To-do app will depend on.
Make a directory called **routes** - `mkdir routes`

`cd routes`
`touch api.js`

![Screenshot from 2022-04-26 21-14-45](https://user-images.githubusercontent.com/34113547/165414754-565f44d2-fce6-4876-aafe-bccbcc7ea7ad.png)

`vim api.js`

![Screenshot from 2022-04-26 21-15-15](https://user-images.githubusercontent.com/34113547/165414798-b3c0a3c2-40bf-4185-8c34-fd9794e54baf.png)

#### Models

We need to create a model because the app is going to make use of Mongodb which is a NoSQL database. We will use models to define the database schema so that we will be able to define the fields stored in each Mongodb document. The Schema is basically a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database known as virtual properties.

Install mongoose - `npm install mongoose`

![Screenshot from 2022-04-26 21-21-10](https://user-images.githubusercontent.com/34113547/165414827-3a979d00-ef07-4ad4-910c-06530e11ec59.png)

Mongoose is Node.js package that makes working with mongodb easier.

`mkdir models && cd models && touch todo.js`

![Screenshot from 2022-04-26 21-22-16](https://user-images.githubusercontent.com/34113547/165414855-e9502599-1c66-437f-84ef-9d9e2dc421a8.png)

`vim todo.js`

![Screenshot from 2022-04-26 21-24-27](https://user-images.githubusercontent.com/34113547/165414877-4b0f39c4-64f2-4731-b6f8-0d56dee33182.png)

Now update the routes in the file api.js in the **routes** directory to make use of the new model

`cd.. && cd routes`

`vim api.js`

#### MongoDB Database

Create a MongoDB database and collection inside mLab using clusters and allow access to the MongoDB database from anywhere.

Create a file in your Todo directory and name it **.env** - `touch .env`

`vim .env`
Edit the file and put your database connection details `DB = 'mongodb+srv://cumi:<password>@cluster0.hmffv.mongodb.net/myFirstDatabase?retryWrites=true&w=majority'`

![Screenshot from 2022-04-26 22-47-06](https://user-images.githubusercontent.com/34113547/165415021-12ac9fd3-0189-4ad8-acaf-6609c9a20676.png)

Update index.js to reflect the use of .env so that Node.js can connect to the database

`vim index.js`

![Screenshot from 2022-04-26 23-16-26](https://user-images.githubusercontent.com/34113547/165415071-20bcfb03-7a4f-4eaa-8bc0-6f3a5f5e019a.png)

Run `node index.js` to start the server.

![Screenshot from 2022-04-26 23-21-56](https://user-images.githubusercontent.com/34113547/165415090-44f1596f-6ce9-4d18-a148-b8ccbf981523.png)

Open Postman and create a POST request

![Screenshot from 2022-04-26 23-27-50](https://user-images.githubusercontent.com/34113547/165415155-48f1fe3e-c0d5-4034-a14a-82f7582132ec.png)

Create a GET request to get all existing items 

![Screenshot from 2022-04-26 23-29-32](https://user-images.githubusercontent.com/34113547/165415207-3b7b9200-4d4d-4c3f-9cd1-21f92c89e018.png)

Create a DELETE request to delete the item created earlier and the create a GET request to ensure it was deleted.

![Screenshot from 2022-04-26 23-30-33](https://user-images.githubusercontent.com/34113547/165415255-ef5324b2-6cf8-439a-a2ca-dff4244d4549.png)
![Screenshot from 2022-04-26 23-30-55](https://user-images.githubusercontent.com/34113547/165415295-04ceb917-41c3-4949-b6ef-2fdac1f80a3d.png)

### STEP 1: Frontend Creation

In the Todo root directory, run `npx create-react-app client`

![Screenshot from 2022-04-26 23-48-35](https://user-images.githubusercontent.com/34113547/165415332-bd9433c8-013b-4bb4-9ef2-724003373a4d.png)

Install the dependencies Concurrently and Nodemon

Run `npm install concurrently --save-dev` and `npm install nodemon --save-dev`

![Screenshot from 2022-04-26 23-57-34](https://user-images.githubusercontent.com/34113547/165415367-6fc79184-4ab8-43f2-bcc8-457faff524ee.png)

Open and edit the package.json file - `vi package.json`

![Screenshot from 2022-04-27 00-08-46](https://user-images.githubusercontent.com/34113547/165415419-36d0c455-d77b-4333-bd2a-ef7e383571e0.png)

Now, configure Proxy in package.json

![Screenshot from 2022-04-27 00-15-18](https://user-images.githubusercontent.com/34113547/165415443-6e00dab4-0484-496f-a475-94a6b88cbcad.png)

`cd client` and `vi package.json`

![Screenshot from 2022-04-27 00-08-46](https://user-images.githubusercontent.com/34113547/165416278-c09a2273-e216-4f00-b303-2e227f4ccb88.png)

`cd ..` and `npm run dev`

Open TCP port 3000 on the ec2 instance by adding a new Security Group rule.

![Screenshot from 2022-04-27 00-31-50](https://user-images.githubusercontent.com/34113547/165416349-90f8371d-dc9e-4e5b-8562-1cd51a0b67da.png)

Next, create React components

`cd client` <br>
`cd src` <br>
`mkdir components` <br>
`cd components` <br>
`touch Input.js ListTodo.js Todo.js`

![Screenshot from 2022-04-27 00-36-51](https://user-images.githubusercontent.com/34113547/165416413-2320e9fa-be87-44c3-bf92-31a0f0842450.png)

`vi Input.js`

![Screenshot from 2022-04-27 00-38-08](https://user-images.githubusercontent.com/34113547/165416445-fd3c79d2-57e2-43a5-9829-48097a073921.png)

Navigate back to the **clients** directory

Install Axios `npm install axios`

![Screenshot from 2022-04-27 00-42-41](https://user-images.githubusercontent.com/34113547/165416478-a7cc473e-0426-45ba-a248-0ff10eacb5d0.png)

Navigate to src/components - `cd src/components`

`vi ListTodo.js`

![Screenshot from 2022-04-27 00-52-21](https://user-images.githubusercontent.com/34113547/165416604-14287bde-ae6f-415b-a464-680eb0899184.png)

`vi Todo.js`

![Screenshot from 2022-04-27 00-53-22](https://user-images.githubusercontent.com/34113547/165416621-53345f57-b18d-4fbd-9ad2-a009c05f1b04.png)

`cd .. && vi App.js`

![Screenshot from 2022-04-27 01-10-58](https://user-images.githubusercontent.com/34113547/165416643-81a611c6-6831-4e87-9c32-b2d9bd0579fc.png)

`vim index.css`
![Screenshot from 2022-04-27 01-14-24](https://user-images.githubusercontent.com/34113547/165416658-d5c131b5-200b-4f08-8c02-1bfaa40dd35c.png)

Navigate back to the **Todo** directory and run `npm run dev`


Open http://localhost3000/ on your browser.

![Screenshot from 2022-04-27 01-28-53](https://user-images.githubusercontent.com/34113547/165416689-62be590e-092d-4670-8ab2-035678bd12d8.png)
