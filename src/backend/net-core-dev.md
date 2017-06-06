# .Net Core Development

This article is about installing and using JDash server libraries on your backend. 

Integrating JDash server libraties into your existing .Net Core Web Api/MVC application is easy.

# Prerequisites

You need to install .Net Core SDK on your server and development machine. For more information please take a look at 
<a href="https://www.microsoft.com/net/core">Microsoft's .NET Core Installation Page.</a>

# Preparing Your Server Side

## Step 1 : Add JDash.NetCore References To Your Project

#### If You Are Using Visual Studio

Create new empty Asp.Net Core MVC Project (MVC chosen for client implementation, you can also use an empty project for only server sided - API calls).

You need to add 2 basic references to your application to get JDash going

    1- JDash.NetCore.Api

To Install JDash.NetCore.Api, all you have to do is installing a nuget package from your nuget package manager console : 

    Install-Package JDash.NetCore.Api

For persistence, we have 2 default providers you can use : 

To Install MSSQL Provider :

    Install-Package JDash.NetCore.Provider.MsSQL
     
To Install MySQL Provider :

    Install-Package JDash.NetCore.Provider.MySQL

If these providers wont fill your needs you can always implement your own provider via interface below:

    JDash.NetCore.Models.IJDashPersistenceProvider


#### If You Are Using other IDE (Like VS Code, JetBrains Rider)

Create a simple web application via 
    
    dotnet new web

Add latest references trough your package manager from nuget

     JDash.NetCore.Api

Add presistence provider reference which suits your application 

    JDash.NetCore.Provider.MsSQL
    JDash.NetCore.Provider.MySQL


If these providers wont fill your needs you can always implement your own provider via interface below:

    JDash.NetCore.Models.IJDashPersistenceProvider

## Step 2 : Implementing JDash.NetCore To Your Application

We made JDash pretty easy and configurable so that you can implement it easily. Mosft of your work done via javascript and this .Net Core libraries are used for persistance and/if needed authorization layer only.

In your Startup.cs file :
 
```csharp
//add below usings above code to your code
using JDash.NetCore.Api; 
using JDash.NetCore.Models;
```

After adding these usings above, just add a single simple line below your "Configure" method.

```csharp
        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
             // .. removed redundant code 

            app.UseDefaultFiles(); // to serve a static index.html file.
            app.UseStaticFiles();
        
            app.UseJDash<JDashConfigurator>(); // add this line to your application
        }

```

## Step 3 : Implementing Configurator and Setting Provider and Autharization System

At step 2, we added this simple line : `app.UseJDash<JDashConfigurator>();` , Now it is time to implement our JDashConfigurator class.

Create a new class file with name JDashConfigurator and derive it from JDash.NetCore.Api.BaseJDashConfigurator

With this class, we can give our configuration to JDash, it will also handle your user authentication for jdash and also this is where you decide to use which Persistence provider you will use.

```csharp
        // add below usings for persistance providers for your requirements
        using JDash.NetCore.Provider.MsSQL; // JSQLProvider 
        using JDash.NetCore.Provider.MySQL; // JMySQLProvider


    public class JDashConfigurator : BaseJDashConfigurator
    { 
        public JDashConfigurator(HttpContext context)
         : base(context)
        {
            this.EnsureTablesCreated = true; // defaults to true.. It will check whether tables are created,
                                             // if not creates them. 
            // You can disable this feature by setting this property false;                                             
        }


        public override JDashPrincipal GetPrincipal(string authorizationHeader)
        {
            var context = this.HttpContext; // gets current context for http request of JDash.
            // You can use authorization header to check current user (see token authorization for JDash). If you are using cookies authentication, you can simply get username from 
            
            // this.HttpContext.User.Identity.Name

            // appid is for multiple applications, if you have only 1 application, you can just write your application name there (appid must be filled).
            return new JDashPrincipal(user : "current user name must be determined here" );
        }

        public override IJDashProvider GetProvider()
        {
            // Here we are using MsSqlProvider for JDash from package 
            // JDash.NetCore.Provider.MsSQL.JSQLProvider

            var provider = new JSQLProvider(connectionString: "some connection string");
            return provider;
        }
    }

```

### JDashPrincipal GetPrincipal(string authorizationHeader)

        With this method, we will call it from our controller when we need a user information. You can either use the default this.HttpContext.User.Identity.Name (forms authentication) or implement your own authentication mechanic for JDash.

### IJDashProvider GetProvider()

        With this method, we will need you to create a persistance provider instance that is implementing IJDashPersistenceProvider. 


### Using providers

Currently JDash supports MSSQL an MySQL to store and retrieve dashboard data.

You can use the ``` BaseJDashConfigurator.GetProvider() ``` method to determine the persistance provider you will use as the implementation details shown at "Step 2: Implementing JDash.NetCore To Your Application"

You can use these built-in packages as reference for your application :

    1- JDash.NetCore.Provider.MsSQL 
    2- JDash.NetCore.Provider.MySQL 


If you want another data provider, you can also implement it yourself by using 

```csharp
        JDash.NetCore.Models.IJDashPersistenceProvider
```

Interface so that you can easily integrate your own provider yourself.


# Using Jdash client UI with your backend

## Step 1: Install JDash UI package
Use npm to install Jdash user interface package. Ensure you are inside ``wwwroot`` folder.

```no-highlight
cd wwwroot
npm install jdash-ui --save
```

Note: If this is the first time you use npm to add a package first execute `npm init` to create package.json file.

## Step 2: Create a new index.html file inside your ``wwwroot`` folder

As we are only interested in server side on this section of tutorial. We will give a static html file sample. You can use mvc/razor to generate dynamic content to suit your needs.

Copy below HTML content inside your index.html file. 

To learn more about client side development is, please see [Client Development](../client/index.md) for examples.

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

    <j-dashlet id="hello-world" title="Hello world!">
        <template>
            <!-- This will be the HTML content of your dashlet  -->
            <h1></h1>
            <div>Naber</div>
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

</head>

<body class="j-light-gray j-padding">
    <button id="createDashboardBtn">Create Dashboard</button>
    <button id="addDashletBtn">Add Dashlet</button>
    <div id="dashboardList"></div>

    <j-dashboard id="dashboard" j-view-mode="dashletedit">
        <h2 j-bind="title"></h2>
    </j-dashboard>

    <script>
        jdash.ready(function () {
            jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' }); // this line here binds your server to client.

            var dashboardlist = document.getElementById("dashboardList");
            var dashboard = document.getElementById("dashboard");
            dashboard.style.display = 'none';

            jdash.Provider.init({
                userToken: function (callback) {
                    callback(null, 'authorizationToken If Will Be Used');
                }
            });

            jdash.Provider.getMyDashboards().then(function (dashboards) {

                for (var i = 0; i < dashboards.data.length; i++) {
                    var dashboardInfo = dashboards.data[i];
                    var newButton = document.createElement("button");
                    newButton.innerText = "Dashboard : " + dashboardInfo.title;
                    newButton.onclick = (function (id) {
                        return function () {
                            dashboard.load(id);
                            dashboard.style.display = '';
                        }
                    })(dashboardInfo.id);

                    dashboardlist.appendChild(newButton);

                }
            });

            document.querySelector('#createDashboardBtn').addEventListener('click', function () {
                var title = window.prompt('Set a title for new dashboard');

                // Create a new dashboard
                jdash.Provider.createDashboard({
                    title: title
                }).then(function (result) {
                    console.log('Dashboard created with id:' + result.id);
                    dashboard.load(result.id);
                    dashboard.style.display = '';
                }).catch(function (err) {
                    alert('There was an error creating dashboard: ' + err.message || err)
                })
            });

            // add hello world dashlet to dashboard
            document.querySelector('#addDashletBtn').addEventListener('click', function (e) {
                dashboard.addDashlet('hello-world');
            });
        });
    </script>
</body>

</html>

```

### Binding events to jdash

For js client to know about your backend endpoint, you just have to write 1 line of javascript code at the beginning of jdash.ready event like below, this will make client provider work with your backend instead of default cloud provider.
 
     jdash.ready(function () {
            jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' }); // this line here binds your 
     });

## Step 4 : Run Your Application

Now everything is ready, it is time to boot your application. Open your command prompt/bash and just write 
        dotnet restore
        dotnet run

or if you have visual studio you can just start your application with F5.

Now go to your http://localhost:{port}/dashboard  page and check your first JDash application. 