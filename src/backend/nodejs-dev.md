# NodeJS Development

This article is about installing and using Jdash server libraries on your backend. If you decide to use Jdash Cloud you can skip this.

Integrate JDash server libraries into your existing NodeJs application is easy. 

## Step 1: Install JDash-Express
Use npm to install Jdash express library.

```no-highlight
npm install jdash-express --save
```

This will create node_modules/jdash-express folder.

Note: If this is the first time you use npm to add a package first execute npm init to create a package.json.

## Step 2: Attaching jdash-express middleware to your application

It is pretty easy to attach jdash your express application via jdash-express npm package. All you have to do is below

```javascript
// require jexpress and load the package
var jexpress = require('jdash-express'); 

// create a router for jdash
var jdashRoutes = express.Router();

// jexpress acts as a middleware for specified route.
// configure jexpress with router and user callback
    jexpress({
        principal: function (request) {
            return {
                user: request.user.id, // user which makes this request
                appid:  "myapp" 
            }
        },
        provider: null // we will explain provider details below.
    }).use(jdashRoutes);
```

Above code sample will attach jexpress api routes to the router you provided. 

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

connection.on('connected',function()  {
    jexpress({
        principal: ....
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
        principal: ....
        provider: jmysql({
            connection: connection
        })
}).use(jdashRoutes);

```
## Step 3: Configuring Jdash UI Provider