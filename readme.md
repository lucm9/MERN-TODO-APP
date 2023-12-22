# Deploying a MERN Stack Application on AWS Cloud

The **MERN** stack is a popular technology stack for building full-stack web applications. The acronym "MERN" stands for MongoDB, Express.js, React.js, and Node.js. Each component in the stack plays a specific role in the development of web applications. Here's an overview of each technology in the MERN stack:

**MongoDB**: MongoDB is a NoSQL database that stores data in a flexible, JSON-like format called BSON (Binary JSON). It is designed to handle large amounts of data and is well-suited for applications with dynamic and evolving schemas.

**Express.js**: Backend Framework Express.js is a web application framework for Node.js. It simplifies the process of building robust and scalable web applications by providing a set of features for handling routes, middleware, and HTTP requests.

**React.js**: Frontend Library React.js is a JavaScript library for building user interfaces. 

**Node.js**: Runtime Environment Node.js is a server-side JavaScript runtime that allows developers to run JavaScript on the server.

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

![1 EC2_creation](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/07e4fd65-391c-443e-afbd-26ddd96b6d1c)

on windows terminal, cd into the directory containing the downloaded keypair file. Run the below commond to log into the instance via ssh:
`ssh -i <private_keyfile.pem>username@ip-address`

![2 Logon_Instance](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/a8c7db12-5dfc-4768-aa9a-be8a2263d2b6)

## Configuring Backend 

Run `sudo apt update -y` and `sudo apt upgrade` to update all default ubuntu dependencies to ensure compatibility during package installation.

![3 Sudo_Update](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/d407b716-1660-4dca-a76b-302be2a9cd52)

![4 Sudo_Upgrade](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/badb8867-7722-43de-80b6-eabd7ec2774c)

Next is `nodejs` installation we need to fetch the location from the ubuntu reposotiry using the following command. `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

![5 Fetch_nodejs_location](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/2ee9b7c4-8ae4-4947-bf4e-4d7c21684cd9)

Run the following command to install nodejs.
`sudo apt-get install -y nodejs`

![6 Nodejs_Installation](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/a83e73d9-b26f-47e8-a05e-0e5043e2c88a)

Verify nodejs has been installed by running `node -v`

![7 Nodejs and Npm_verify](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/923c73ec-562a-4d93-9572-c8ea3f752afe)

Installing nodejs will install the package manager as well which is `npm` package manager.

## Setting up the application

We need to create a directory that will house our codes and packages and all the subdirectories to represent components of our application.
`mkdir Todo`

inside that directory we will initialize our project using `npm init`. This enables javascript to install packages usefull for running our application.

![8 npm_init](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/449534fc-a7de-4fbc-8d0a-6a99d0255979)

## Express Installation

We will be installation express which is the backend framework for nodejs. Which will be helping when creating routes for application and http requests. 

![9 Express_Installation](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/f6221ceb-44a3-4702-8e76-9104bb0c7638)

###  "routes" refer to the definition of how an application responds to client requests at specific endpoints (URLs).

Now we create `index.js` file which will contain code useful to run our express server. 

run `touch index.js`

![10 Index_js](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/893f61a1-5e30-4239-9909-0a64468b8002)

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

![11 Opening_port_5000](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/fa24c989-30a8-4cb2-841b-c0ffdc3e7ec5)

## Defining Routes For Our Application

We will create a `routes` folder which will contain code pointing to three main endspoints used in a todo application. This will contain the post,get,delete requests which will be helpful in interacting with our client_side and the database side via restful apis.

```
mkdir routes
cd routes
touch api.js
vi api.js
```

Write the below code into api.js file. It is an example of a simple route that fires to various endpoints. 

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

## Creating Models 

We will creating Models which are often used to interact with databases. In many web frameworks, an ORM (Object-Relational Mapping) or ODM (Object-Document Mapping) is used to map between the database and the application's models. This allows developers to interact with the database using objects and methods rather than raw SQL queries.

Inside the `Todo` directory run `npm install mongoose` to install mongoose. 

Create `models` directory and then create a file `todo.js`. Write the below code into the `todo.js` file

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

Since we have defined our database schema of how our database should be structured we need to then update the `api.js` code in our `route` directory.

```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```
## Creating A MongoDB Database

We will need to create a database to store all the information when we make post request to an endpoint. We will be using MLab which provides DBaas (Database as a service) solution

Login into MLab and create a cluster. 

To connect to the mongoose application database to our database service we connect to it by using the connection credentials provided by mLab.

![12 Creating_Cluster](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/0ab18fb3-7fb1-4321-be6a-03447e8f23f6)

![13 DB_Creation](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/b766e000-8fc9-4ba4-aec7-f364c0abaf66)


Copy the following code and save it insive `.env` file which ![Uploading 12.Creating_Cluster.png…]()
should be created in the parent `todo` directory.

![14 Database_connection](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/79abc2a8-5915-455b-93be-1ef0792b87b8)

`DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`

Change the username,password,database name and network address to the one specified by the mongoose credentials and when creating the database and collection

Update the code in our `index.js` file under the `ToDo` directory as we need to point mongoose to the database service we created using mLab.

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

We run `node index.js` to test our connection and our latest changes to the code.

![15 Testing_Index_js](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/e2028255-5616-4ba9-8e6c-6e3dec4ffbb8)

## Testing Backend Code Using Postman

We have built the backend of our application and in order to test to see if it works without a frontend, we use `Postman` to test the endpoints. 

On Postman, we make a `POST` request to our database specifying an action in the body of request.

![16 Postman_Post_Request](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/1d0fdc4a-2e5b-4769-bbe5-ceef96a5f585)

![17 Get_Request](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/a460ff25-bd07-4084-be4b-17a0ee2e3816)

![18 Validate_In_DB](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/afc5d807-d3fd-450f-83b2-b9fa14af3b8d)

![19 Delete_Request](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/952c356e-6b63-47ed-8f1d-651076e9446a)

## Creating Frontend

In the todo directory which is same directory containing the backend code.
Run `npx create-react-app client`- This creates a client directory containing all the necessary packages required for `REACT` to work.

![20 React_Client](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/d45e9494-e2a7-477d-8b79-1363b4cc4f13)

We install `concurrently` and `nodemon` which are important packages used for the build up process. `Concurrently` ensures multiple command can run at the same time on the same terminal.

```
npm install concurrently --save-dev

npm install nodemon --save-dev
```

![21 Concurrently_nodemon_Install](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/240f4088-d7a6-4c54-afe5-bbf74720b8dc)

Configure the package.json file to run the new installation.

![22 Package_json](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/3306df9c-81ce-4672-9b2e-cf6d359408fc)

Configure the proxy in package.json under the `client` directory to ensure our site is using `http://localhost:5000` rather than always including the entire path like `http://localhost:5000/api/todos`

![23 Package_json_client](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/e67f5901-a77a-4caa-ad4e-31426fe107c3)

We move into the `Client` directory then cd into `src` directory amd them create a `components` directory which will contain files that contains our frontend code. 

Inside the `component` directory we create files such as `input.js`, `ListTodo.js`, `Todo.js`

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
```

To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your `client`directory from your terminal and run `npm install axios`.

In the `ListTodo.js`

```
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```

in the Todo.js 

```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```

Under `src` directory `touch api.js` in the file copy and paste the following
```
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```

Open the `app.css` file and update with the following
```
.App {
    text-align: center;
    font-size: calc(10px + 2vmin);
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    }
    
    input {
    height: 40px;
    width: 50%;
    border: none;
    border-bottom: 2px #101113 solid;
    background: none;
    font-size: 1.5rem;
    color: #787a80;
    }
    
    input:focus {
    outline: none;
    }
    
    button {
    width: 25%;
    height: 45px;
    border: none;
    margin-left: 10px;
    font-size: 25px;
    background: #101113;
    border-radius: 5px;
    color: #787a80;
    cursor: pointer;
    }
    
    button:focus {
    outline: none;
    }
    
    ul {
    list-style: none;
    text-align: left;
    padding: 15px;
    background: #171a1f;
    border-radius: 5px;
    }
    
    li {
    padding: 15px;
    font-size: 1.5rem;
    margin-bottom: 15px;
    background: #282c34;
    border-radius: 5px;
    overflow-wrap: break-word;
    cursor: pointer;
    }
    
    @media only screen and (min-width: 300px) {
    .App {
    width: 80%;
    }
    
    input {
    width: 100%
    }
    
    button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
    }
    }
    
    @media only screen and (min-width: 640px) {
    .App {
    width: 60%;
    }
    
    input {
    width: 50%;
    }
    
    button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
    }
    }
```

Open the `index.css` file and update with the following
```
body {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    box-sizing: border-box;
    background-color: #282c34;
    color: #787a80;
    }
    
    code {
    font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
    }
```

Lastly go into the root directory `Todo` and run `npm run dev`. This builds the application and spins it up
![24 npm_dev](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/7d841620-ac2c-486f-8c32-57331bf6eab9)

![25 Todo_Application](https://github.com/lucm9/MERN-TODO-APP/assets/96879757/cfdf7e4e-9219-4ca0-b8da-24afe737b9cf)


