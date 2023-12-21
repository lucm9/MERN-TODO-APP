# Deploying a MERN Stack Application on AWS Cloud

The MERN stack is a popular technology stack for building full-stack web applications. The acronym "MERN" stands for MongoDB, Express.js, React.js, and Node.js. Each component in the stack plays a specific role in the development of web applications. Here's an overview of each technology in the MERN stack:

## MongoDB: MongoDB is a NoSQL database that stores data in a flexible, JSON-like format called BSON (Binary JSON). It is designed to handle large amounts of data and is well-suited for applications with dynamic and evolving schemas.

## Express.js: Backend Framework Express.js is a web application framework for Node.js. It simplifies the process of building robust and scalable web applications by providing a set of features for handling routes, middleware, and HTTP requests.

## React.js: Frontend Library React.js is a JavaScript library for building user interfaces. 

## Node.js: Runtime Environment Node.js is a server-side JavaScript runtime that allows developers to run JavaScript on the server.

When combined, the MERN stack allows developers to build end-to-end web applications using JavaScript for both the frontend and the backend. Here's a typical flow of how these technologies work together:

- **Backend Flow:**
  1. Node.js with Express handles HTTP requests from the client.
  2. Express routes the requests to the appropriate endpoints.
  3. Express interacts with the MongoDB database to retrieve or modify data.

- **Frontend Flow:**
  1. React.js components render the user interface in the browser.
  2. React.js manages the state of the application and sends requests to the Node.js backend through API endpoints.
  3. Data is retrieved from the MongoDB database and displayed in the React.js components.

We will build a simple todo list application and deploy it onto AWS cloud EC2 machine. 

## Creating EC2 instances 

We log on to AWS cloud Services and create an EC2 Ubuntu VM instance. When creating an instance, choose keypair authentication and download private key(.*pem) on your local computer.

![Alt text](<Screenshot 2023-12-20 235734.png>)

on windows terminal, cd into the directory containing the downloaded keypair file. Run the below commond to log into the instance via ssh:
`ssh -i <private_keyfile.pem>username@ip-address`

![Alt text](<Screenshot 2023-12-21 003537.png>)

## Configuring Backend 

Run `sudo apt update -y` and `sudo apt upgrade` to update all default ubuntu dependencies to ensure compatibility during package installation.

Next is `nodejs` installation we need to fetch the location from the ubuntu reposotiry using the following command. `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`


Run the following command to install nodejs.
`sudo apt-get install -y nodejs`

Verify nodejs has been installed by running `node -v`

Installing nodejs will install the package manager as well which is `npm` package manager.

## Setting up the application

We need to create a directory that will house our codes and packages and all the subdirectories to represent components of our application.
`mkdir Todo`

inside that directory we will initialize our project using `npm init`. This enables javascript to install packages usefull for running our application.

## Express Installation

We will be installation express which is the backend framework for nodejs. Which will be helping when creating routes for application and http requests. 

###  "routes" refer to the definition of how an application responds to client requests at specific endpoints (URLs).

Now we create `index.js` file which will contain code useful to run our express server. 

run `touch index.js`

Next we install `dotenv` This command installs the dotenv package and adds it to your project's dependencies in the `package.json` file. 

run `vi index.js` inside this file we type the following code

run `npm install dotenv`

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});

```

run `node index.js` to get the server up and running. Running `node index.js` is a command used to execute a `Node.js` script. Assuming you have a file named `index`.js in your current directory, running this command will start the Node.js application defined in that file.

## Security Group

Notice in the index.js file we are exposing port number `5000`. There for we need to create an inbound rule allowing requests to that port. 

paste the public-ip:5000 onto the brower. We should get the message `welcome to express`

## Defining Routes For Our Application

We will create a route 

