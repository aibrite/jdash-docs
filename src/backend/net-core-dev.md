# .Net Core Development

This article is about installing and using Jdash NetCore libraries on your backend. 

If you want to use Jdash Cloud to store and manage dashboard related data you can continue from [Getting Started](../client/getting-started.md) section.

## Prerequisites

Before preceding this tutorial ensure you have installed .Net Core SDK on your development machine. For more information see
<a target="_blank" href="https://www.microsoft.com/net/core">Microsoft's .NET Core Installation Page.</a>

## Step 1 : Add JDash.NetCore references 

### If you are using Visual Studio

Use File | New Project | Asp.Net Core Web Application menu to create an empty project.

Use Nuget Package Manager Console window to add Jdash NetCore library references to your project.  

```no-highlight
Install-Package JDash.NetCore.Api
Install-Package JDash.NetCore.Provider.MsSQL
Install-Package JDash.NetCore.Provider.MySQL
Install-Package Microsoft.AspNetCore.StaticFiles
```

### If you are using code editors
If you are using Vs Code or similar code editors you can use ``dotnet`` command to create a basic web application structure.

```no-highlight
dotnet new web
```

This will create an application with default configuration.

Open .csproj file and add Jdash references.

```xml
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
    
    <!-- Add below references -->
    <PackageReference Include="JDash.NetCore.Api" Version="*" />
    <PackageReference Include="JDash.NetCore.Provider.MsSQL" Version="*" />
    <PackageReference Include="JDash.NetCore.Provider.MySQL" Version="*" />

    <!-- We also need this assembly to serve static files for this tutorial -->    
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.2" />

</ItemGroup>
```
## Step 2: Implement configuration class
Jdash NetCore uses a configuration class to configure authentication and provider settings. 

Below is a sample configuration class.

```csharp

public class JDashConfig: BaseJDashConfigurator {

    public JDashConfigurator(HttpContext context) : base(context)
    {
    }

    // Use this method to get current user for current request.
    public override JDashPrincipal GetPrincipal()
    {            
        return new JDashPrincipal("current-user");
    }

    // Jdash NetCore library calls this method 
    // to get a provider instance.
    public override IJDashProvider GetProvider()
    {
         // Ensure you have a valid database.
         
        string connectionString = "Your SQL Server connection string";
        return new JSQLProvider(connectionString);

        // if you are using MySql uncomment below lines.
        // string mySqlConnStr = "Server=127.0.01;Database=jdash;Uid=root;Pwd=1;";
        // return new JDash.NetCore.Provider.MySQL.JMySQLProvider(mySqlConnStr);
    }
}

```

## Step 3: Registering Jdash Api end points
Open Startup.cs and paste below namespaces first. This will add extension methods.

```csharp
using JDash.NetCore.Api; 
using JDash.NetCore.Models;
```

Locate ``Configure`` method of your ``Startup`` class and replace it as below.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    loggerFactory.AddConsole();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    // use index.html as default.
    app.UseDefaultFiles(); 

    // serve static content.
    app.UseStaticFiles(); 

    // register Jdash routes.
    app.UseJDash<JDashConfig>(); 
}

```
Also call AddMvc extension method inside ConfigureServices.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
} 
```

## Step 4: Restoring packages and test run

Use ``dotnet restore`` command to restore project references. After succesfully restoring references use ``dotnet run`` to run your project. 

```no-highlight
dotnet restore
dotnet run
```

Now you should see an  output as below.

```no-highlight
Hosting environment: Production
Content root path: /Users/aibrite/project/jdash/jdash-netcore-tutorial
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

## Step 5: Client side development
Use npm to install Jdash user interface package. Ensure you are inside ``wwwroot`` folder.

```no-highlight
cd wwwroot
npm install jdash-ui --save
```

Note: If this is the first time you use npm to add a package first execute `npm init` to create package.json file.

This will create wwwroot/node_modules/jdash-ui folder.

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

### Step 8: View dashboards
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

## Step 9: Run your application

Now go to your http://localhost:5000 and enjoy JDash. 

### Step 10: Download source code
Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-netcore-tutorial).



