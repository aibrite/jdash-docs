# .Net Core Development

This article is about installing and using JDash server libraries on your backend. 

Integrating JDash server libraties into your existing .Net Core Web Api/MVC application is easy.

# Prerequisites

You need to install .Net Core SDK on your server and development machine. For more information please take a look at 
<a href="https://www.google.com.tr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjWxeiE1abUAhUCI1AKHbt-AwsQFggoMAA&url=https%3A%2F%2Fwww.microsoft.com%2Fnet%2Fcore&usg=AFQjCNF5LT5GJQX-gEcZ9hsYN4ls1aAsdA">Microsoft's .NET Core Installation Page.</a>

# Preparing Your Server Side

## Step 1 : Add JDash.NetCore References To Your Project

### If You Are Using Visual Studio

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


### If You Are Using other IDE (Like VS Code, JetBrains Rider)

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
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });

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


# Preparing Client Side

This section of this guide is for .Net MVC. The purpose is to show you how to create a simple "Hello World" Dashlet with support by your backend.

## Step 1 : Install JDash Client
Use npm to install Jdash client library, inside your ``wwwroot`` folder.

        npm install --save https://github.com/aibrite/jdash-ui.git

This will create node_modules/jdash-ui folder.

Note: If this is the first time you use npm to add a package first execute npm init to create a package.json.

## Step 2 : Create a Dashboard Controller And Its View

The controller is as simple as this is :

```c# 
       public class DashboardController : Controller
    { 
        public IActionResult Index()
        {
            return View();
        }
    }
```

Below we will add jdash js and our components to the view
Your view (Dashboard/Index.cshtml) should be like : 

```html
@{
    Layout = null;
}

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

## Step 3 : Write your "Hello-World" dashlet

To start your first dashlet definition you can write your dashlet definition at the bottom of head section of your html like this, below code will add a simple hello world dashlet definition. The initialize method will be called for each instance of dashlet.


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

</body>

</html>

```

### Adding Your Buttons to control JDash

Insert these lines to your html body 


```html
<body class="j-light-gray j-padding">
    <button id="createDashboardBtn">Create Dashboard</button>
    <button id="addDashletBtn">Add Dashlet</button>
    <div id="dashboardList"></div>
</body>
```

### Binding events to jdash

Add these srcripts at the bottom your body.

For your OnPremise server provider , you need to add the below line for your application.

     jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' });

url : '/jdash/api/v1'  is default route listener for your server. You need to configure this on both here and server side (app.UseJDash<>()  takes a parameter for listening path ) to change this endpoint.


```javascript 
 <script>
        jdash.ready(function () {
            var dashboardlist = document.getElementById("dashboardList"); 
            var dashboard = document.getElementById("dashboard"); // our j-dashboard instance
            dashboard.style.display = 'none'; // hide dashboard for initialization

            jdash.Provider = new jdash.ProviderTypes.OnPremise({ url: '/jdash/api/v1' }); // defining server provider
            jdash.Provider.init({ // init function must be called
                userToken: function (callback) {  // this method must be implemented and this will allow you to create your
                // authorization header with "Bearer " prefix for jdash requests.
                // You do not have to specify authorization if you are not using stateless http or cookie authentication
                    callback(null, 'authorization token If Will Be Used'); // callback must be called with first parameter is error info (null if no error). Second parameter can be empty string or a server generated authorization token.
                }
            });

            jdash.Provider.getMyDashboards().then(function (dashboards) { // get dashboards of current user

                for (var i = 0; i < dashboards.data.length; i++) {
                    var dashboardInfo = dashboards.data[i];
                    var newButton = document.createElement("button");
                    newButton.innerText = "Dashboard : " + dashboardInfo.title;
                    newButton.onclick = (function (id) {
                        return function () {
                            dashboard.load(id); // load the dashboard
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
                    dashboard.load(result.id); // load created dashboard
                    dashboard.style.display = '';
                }).catch(function (err) {
                    alert('There was an error creating dashboard: ' + err.message || err)
                })
            });

            // add hello world dashlet to dashboard
            document.querySelector('#addDashletBtn').addEventListener('click', function (e) {
                dashboard.addDashlet('hello-world'); // add dashlet to both client and server persistence.
            });
        });
    </script>

```


## Step 4 : Run Your Application

Now everything is ready, it is time to boot your application. Open your command prompt/bash and just write 
        dotnet restore
        dotnet run

or if you have visual studio you can just start your application with F5.

Now go to your http://localhost:{port}/dashboard  page and check your first JDash application.