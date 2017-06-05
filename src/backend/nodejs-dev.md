# NodeJS Development

This article is about installing and using Jdash NodeJs modules on your backend. If you decide to use Jdash Cloud you can skip this.


## Step 1: Initial setup
Use npm to install Jdash NodeJs module.

```no-highlight
npm install jdash-express --save
```

This will create node_modules/jdash-express folder. 

Note: If this is the first time you use npm to add a package first execute npm init to create a package.json.

Jdash uses <a target="_blank" href="http://expressjs.com">Express</a>. Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

### Initial application

Below code snippet creates a simple NodeJs application which serves static files from `www`folder.

```javascript
var path = require('path');
var express = require('express');
var bodyParser = require('body-parser');
var jexpress = require('jdash-express').default; 

// Create an app
var app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, 'www')));



// Start listening port 3000
app.listen(3000, function () {

})
```

## Step 2: Attaching jdash-express middleware to your application

It is pretty easy to attach jdash routes to your express application. 

```javascript
// create a router for jdash
var jdashRoutes = express.Router();

// jexpress acts as a middleware for specified route.
// configure jexpress with router and user callback
jexpress({
    principal: function (request) {
        return {
            user: request.user.id, // username which makes this request
            appid: "myapp" // application id of this app
        }
    },
    provider: null // we will explain provider details below.
}).use(jdashRoutes);

// Use this router
app.use('/jdash/api/v1', jdashRoutes);


app.listen(3000, function () {

})
```

### Setting a principal and application name
Use `principal` property of configuration object to set a user name for current request. This value is used to get the user which makes Jdash api request.

You also need to provide an application name. Application name allows to use same database instance for different applications. 

### Using providers
Providers are NodeJs modules which allows to retrieve and persist dashboard data i.e. dashlet configurations, layouts and so on.

Currently Jdash has two providers for MongoDB and MySQL.

#### Using MongoDB with Jdash 

Use npm to install Jdash MongoDb provider.
```no-highlight
npm install jdash-mongodb --save
```

```typescript
var mongoose = require('mongoose');
var jmongo = require('jdash-mongodb').default;

var connStr = 'Your connection string goes here';
var connection = mongoose.createConnection(connStr);

// Configure Jdash when database connection is ready.
connection.on('connected', function () {
    jexpress({
        principal: function (request) {
            return {
                user: request.user.id, 
                appid: "myapp" 
            }
        },
        // Set provider
        provider: jmongo({
            connection: connection
        })
    }).use(jdashRoutes);
})
```

#### Using MySQL with Jdash
Use npm to install Jdash MySQL provider.
```no-highlight
npm install jdash-mysqldb --save
```

```typescript
var mysql = require('mysql');
var jmysql = require('jdash-mysqldb').default;

var connection = mysql.createPool({
    connectionLimit: 100,
    host: 'localhost',
    port: 3306,
    user: 'root',
    password: '1234',
    database: 'jdash_local'
});

jexpress({
        principal: function (request) {
            return {
                user: request.user.id, 
                appid: "myapp" 
            }
        },
        provider: jmysql({
            connection: connection
        })
}).use(jdashRoutes);
```

## Step 3: Configuring Jdash UI Provider