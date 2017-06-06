# NodeJS Development

This article is about installing and using Jdash NodeJs modules on your backend. 

If you want to use Jdash Cloud for storing dashboard related data you can continue from [Getting Started](../client/getting-started.md) section.


## Step 1: Initial setup
Use npm to install Jdash NodeJs packages.

```no-highlight
npm install jdash-express --save
```

Note: If this is the first time you use npm to add a package first execute `npm init` to create a package.json.

Jdash uses <a target="_blank" href="http://expressjs.com">Express</a>. Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

### Basic server application

Below code snippet creates a simple NodeJs application which serves static files from root folder.

```javascript
var express = require('express');
var bodyParser = require('body-parser');

// Create an app
var app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(express.static(__dirname));


// Start listening port 3000
app.listen(3000, function () {

})
```

## Step 2: Attaching jdash-express middleware to your application

```javascript
var jexpress = require('jdash-express').default;

// create a router for jdash
var jdashRoutes = express.Router();

// jexpress acts as a middleware for specified route.
// configure jexpress with router and user callback
jexpress({
    principal: function (request) {
        return {
            user: "current-user", // username which makes this request
            appid: "myapp" // application id of this app
        }
    },
    provider: null // we will explain provider details later.
}).use(jdashRoutes);

// Use this router
app.use('/jdash/api/v1', jdashRoutes);

app.listen(3000, function () {

})
```

### Setting a principal and application name
Use `principal` property of `jexpress` configuration object to get user from current request. This value is used to set the user name property of dashboards and dashlets inside database so that each user can have their own dashboards and dashlets.

You also need to provide an application name. Application name allows to use same database instance for different applications. 

### Using providers
Providers are NodeJs packages which allows to retrieve and persist dashboard data i.e. dashlet configurations, layouts and so on.

Currently Jdash has two builtin NodeJs packages for MongoDB and MySQL.

#### Using MongoDB with Jdash 

Use npm to install Jdash MongoDb NodeJs package.

```no-highlight
npm install jdash-mongodb --save
```

```typescript
var mongoose = require('mongoose');
var jmongo = require('jdash-mongodb').default;

var connStr = 'Your connection string goes here';

// Create a database connection
var connection = mongoose.createConnection(connStr);

// Configure Jdash when database connection is ready.
connection.on('connected', function () {
    jexpress({
        principal: function (request) {
            return {
                user: "current-user", 
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

// Create a database connection
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
                user: "current-user", 
                appid: "myapp" 
            }
        },
        provider: jmysql({
            connection: connection
        })
}).use(jdashRoutes);
```

## Step 3: Client side development
Use npm to install Jdash UI package.

```no-highlight
npm install jdash-ui --save
```

This will create node_modules/jdash-ui folder.

Create index.html inside your project root and paste below code.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
    <title>JDash Tutorial</title>

    <!-- jdash client library -->
    <script src="node_modules/jdash-ui/dist/jdash.min.js"></script>

    <!-- jdash theme & elements -->
    <link rel="import" href="node_modules/jdash-ui/dist/components/jdash.html">
</head>

<body class="j-light-gray j-padding">

</body>

</html>
```

### Step 4: Develop your first dashlet
You use `j-dashlet` element to define a dashlet. 

Content of `j-dashlet` can include `template` element which can be used to define dom (innerHTML) of your dashlet. An optional `script` element inside `j-dashlet` can be used to execute javascript for this dashlet. 

Copy the following code inside body tag. 

```html
<!-- Define <hello-world> dashlet -->
<j-dashlet id="hello-world" title="Hello world!">
    <template>
        <!-- This will be the HTML content of your dashlet  -->
        <h1></h1>
    </template>
    <script>
        jdash.define(function () {

            this.initialized = function () {
                // Dom is ready!
                var h1 = this.querySelector('h1')
                h1.textContent = 'Hello World!';
            }

        })
    </script>
</j-dashlet>
```

### Step 5: Create and display dashboard
Use `j-dashboard` element to display a dashboard. 

Inside `body` tag add following code.

```html
    <button id="createDashboardBtn">Create Dashboard</button>
    <button id="addDashletBtn">Add Dashlet</button>
    <div id="dashboardList"></div>

    <j-dashboard id="dashboard" j-view-mode="dashletedit">
        <h2 j-bind="title"></h2>
    </j-dashboard>
</body>
```

Add a `script` element after `body` and paste below code.

```html
<script>
    jdash.ready(function () {

        // Set provider
        jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' }); // define your end point

        // createDashboardList();
        document.querySelector('#createDashboardBtn').addEventListener('click', function () {
            var title = window.prompt('Set a title for new dashboard');
 
            // Create a new dashboard
            jdash.Provider.createDashboard({
                title: title
            }).then(function (result) {
                console.log('Dashboard created with id:' + result.id);
                dashboard.load(result.id);
                //createDashboardList();
            }).catch(function (err) {
                alert('There was an error creating dashboard: ' + err.message || err)
            })
        })

        // add hello world dashlet to dashboard
        document.querySelector('#addDashletBtn').addEventListener('click', function (e) {
            dashboard.addDashlet('hello-world');
        })
    })
</script>
</body>
```

### Step 6: View dashboards
As last step we will implement creating buttons to view existing dashboards.

`jdash.Provider.getMyDashboards` method returns a list of dashboards current user owns. Note that you initialize current credentials by setting a valid value for `userToken` param for `jdash.Provider.init` method.

Add following function inside `script` element.

```javascript
function createDashboardList() {
    var container = document.querySelector('#dashboardList');

    // clear existing buttons.
    container.innerHTML = '';
    return jdash.Provider.getMyDashboards().then(function (result) {

        // for each dashboard create a button
        for (var i = 0; i < result.data.length; i++) {
            var btn = document.createElement('button');
            btn.textContent = result.data[i].title;
            btn.id = result.data[i].id;
            container.appendChild(btn);
            btn.addEventListener('click', function (e) {
                var idToLoad = e.target.id;
                dashboard.load(idToLoad);
            })
        }
    })
}
```
Uncomment this function when page loads / user creates a new dashboard so that dashboard list is updated.

### Step 7: Download source code
Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-nodejs-tutorial).

If you enjoy Jdash you can continue from Basic Concepts.