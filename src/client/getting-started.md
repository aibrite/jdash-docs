# Getting Started

## Quick Start
The aim of this article is to get you up and running with Jdash in 5 minutes.

### Step 1: Install JDash Client
Use npm to install Jdash client library.

```no-highlight
npm install jdash-ui --save
```

This will create node_modules/jdash-ui folder.

Note: If this is the first time you use npm to add a package first execute npm init to create a package.json.

### Step 2: Create a basic HTML page
Create index.html and paste below code inside.

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

### Step 3: Develop your first dashlet
You use `j-dashlet` element to define a dashlet. 

Content of `j-dashlet` can include `template` element which can be used to define dom (innerHTML) of your dashlet. An optional `script` element inside `j-dashlet` can be used to execute javascript for this dashlet. That simple & clear!

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

### Step 4: Get a free api key
JDash cloud can save and retrieve user data i.e. dashboards, layouts, dashlet configurations and positions.

To use Jdash Cloud <a href="https://app.jdash.io/#!/app/account/register" target="_blank">register as a new user</a>. After registration you will get an api key and test user token. Use them to initialize authentication.

Todo so, add a `script` element just before `</body>` tag.

```html
    <script>
        jdash.ready(function () {
            jdash.Provider.init({
                apiKey: 'YOUR API KEY',
                userToken: 'USER TOKEN'
            })
        })
    </script>
</body>
```
apikey
:  A string value which identifies your application.

userToken
:  A string value or function to authenticate your end user to Jdash cloud.

### Step 5: Create and display dashboard
You use `j-dashboard` element to display a dashboard. 

Inside `body` tag, before `<script>` element add following code.

```html
<button id="createDashboardBtn">Create Dashboard</button>
<button id="addDashletBtn">Add Dashlet</button>
<div id="dashboardList"></div>

<j-dashboard id="dashboard" j-view-mode="dashletedit">
    <h2 j-bind="title"></h2>
</j-dashboard>
```

We also added some buttons to create dashboard and adding dashlets.

Now and event handlers inside `jdash.ready` handler.

```html
<script>
    jdash.ready(function () {

        jdash.Provider.init({
            apiKey: 'YOUR API KEY',
            userToken: 'USER TOKEN'
        })

        document.querySelector('#createDashboardBtn').addEventListener('click', function () {
            var title = window.prompt('Set a title for new dashboard');
 
            // Create a new dashboard
            jdash.Provider.createDashboard({
                title: title
            }).then(function (result) {
                console.log('Dashboard created with id:' + result.id);
                dashboard.load(result.id);
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
Call this function when page loads or user creates a new dashboard.

### Step 7: Download source code
Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-tutorial).

If you enjoy Jdash you can continue from Basic Concepts.