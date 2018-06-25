# .Net Core Development

This article is about installing and using JDash .NetCore libraries on your backend. 

## Prerequisites

Jdash supports both Asp.Net Core 1.x and 2.x.

Before proceeding with this tutorial ensure you have installed .Net Core SDK on your development machine. For more information see
<a target="_blank" href="https://www.microsoft.com/net/core">Microsoft's .NET Core Installation Page.</a>

> Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-netcore-tutorial).

## Step 1 : Add JDash .NetCore package

### If you are using Visual Studio

Use File | New Project | Asp.Net Core Web Application menu to create an empty project.

Use `Add Packages` menu item to add following Jdash packages to your project.

```no-highlight
JDash.NetCore.Api
JDash.NetCore.Models
JDash.NetCore.Provider.MsSQL
JDash.NetCore.Provider.MySQL
```

For demonstration purposes, we also need to add the following package. Note that this package is not a dependency of JDash.

```no-highlight
Microsoft.AspNetCore.StaticFiles
```
### If you are using code editors
Use ``dotnet`` command to create an empty web application.

```no-highlight
dotnet new web
```

Execute following commands to add Jdash package references.

```no-highlight
dotnet add package JDash.NetCore.Api 
dotnet add package JDash.NetCore.Models 
dotnet add package JDash.NetCore.Provider.MsSQL 
dotnet add package JDash.NetCore.Provider.MySQL 
```

Note: This will install .Net Core 2.x libraries. To use .Net Core 1.x Jdash packages use:

```no-highlight
dotnet add package JDash.NetCore.Api --version 1.*
dotnet add package JDash.NetCore.Models --version 1.*
dotnet add package JDash.NetCore.Provider.MsSQL --version 1.*
dotnet add package JDash.NetCore.Provider.MySQL --version 1.*
```

For demonstration purposes, we also need to add the following package. Note that this package is not a dependency of JDash.

```no-highlight
dotnet add package Microsoft.AspNetCore.StaticFiles
```

## Step 2: Implement configuration class
JDash .NetCore uses a configuration class in order to configure authentication and provider settings. 

Below is a sample configuration class.

Create a new file called ``JDashConfig.cs`` and paste the below content inside.

```csharp

using JDash.NetCore.Api;
using JDash.NetCore.Models;
using Microsoft.AspNetCore.Http;

public class JDashConfig : BaseJDashConfigurator {

        public JDashConfig(HttpContext context) : base(context)
        {
        }

        // Use this method to get current user for current request.
        public override JDashPrincipal GetPrincipal(string authorizationHeader)
        {
            return new JDashPrincipal("tutorial-user");
        }

        // Jdash NetCore library calls this method 
        // to get a provider instance.
        public override IJDashProvider GetProvider()
        {
            // Ensure you have a valid database.

            // *** TO USE MSSQL ** //
            //string msSqlConnStr = "Server=10.0.2.15;Database=JDashTutorial;User Id=sa;Password=1.";
            //return new JDash.NetCore.Provider.MsSQL.JSQLProvider(msSqlConnStr);

            // *** TO USE MYSQL ** //
            string mySqlConnStr = "Server=127.0.0.1;SslMode=none;Database=jdash;Uid=root;Pwd=1;";
            return new JDash.NetCore.Provider.MySQL.JMySQLProvider(mySqlConnStr);
        }
}

```

## Step 3: Registering JDash Api endpoints
Open ``Startup.cs`` and add Jdash namespace.

```csharp
using JDash.NetCore.Api; 
```

Locate ``Configure`` method of your ``Startup`` class and add ``UseJDash`` and ``UseStaticFiles()`` methods. 

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    // use index.html as default.
    app.UseDefaultFiles();

    // serve static content.
    app.UseStaticFiles();

    // add Jdash end points
    app.UseJDash<JDashConfig>(); 
}

```
Also call ``AddMvc`` extension method inside ConfigureServices.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
} 
```

## Step 4: Restoring packages and test run

Use ``dotnet restore`` command to restore project references. After succesfully restoring references, use ``dotnet run`` to run your project. 

```no-highlight
dotnet restore
dotnet run
```

## Step 5: Client side development
Use ``npm`` to install JDash User Interface package. Ensure you are inside ``wwwroot`` folder.

```no-highlight
cd wwwroot
npm install jdash-ui --save
```

Note: If this is the first time you use npm to add a package, first execute `npm init` to create ``package.json`` file.

This will create ``wwwroot/node_modules/jdash-ui`` folder.

Create index.html inside wwwroot folder and paste below code.

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

### Step 6: Develop your first dashlet
You use `j-dashlet` element to define a dashlet. 

Content of `j-dashlet` can include `template` element which can be used to define dom (innerHTML) of your dashlet. An optional `script` element inside `j-dashlet` can be used to execute Javascript for this dashlet. 

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

### Step 7: Create and display dashboard
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

Add a `script` element just before the closing `body` and paste below code.

```html
<script>
    jdash.ready(function () {

        // Set provider
        jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' }); // define your endpoint

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

### Step 8: View dashboards
As the last step, we will implement the code to view existing dashboards.

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
Note that the ``createDashboardList()`` function is commented on Step 7. Uncomment this function after Step 8, so that the when page loads / user creates a new dashboard, dashboard list is updated.

## Step 9: Run your application

Now go to your <a target="_blank" href="http://localhost:5000">http://localhost:5000</a> or a port you have previously configured and enjoy JDash. 

## Sample application
Jdash also provies a full featured open source sample application using .Net Core. 

Use <a target="_blank" href="https://github.com/aibrite/jdash-netcore-demoapp">GitHub Repository</a> to download source code of sample application.


