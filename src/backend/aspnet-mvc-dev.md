# Asp.Net MVC Development

This article is about installing and using JDash Asp.Net MVC libraries on your backend. 

If you would like to use JDash Cloud to store and manage dashboard related data you can continue from [Getting Started](../client/getting-started.md) section.

## Step 1 : Add JDash.NetFramework references 

Use File | New Project | Asp.Net Web Application menu to create an empty project.

Use NuGet Package Manager Console window to add JDash .NetFramework library references to your project.  

```no-highlight
Install-Package JDash.NetFramework.Api
Install-Package JDash.NetFramework.Provider.MsSQL
Install-Package JDash.NetFramework.Provider.MySQL 
```

## Step 2: Implement configuration class
JDash .NetFramework uses a configuration class, in order to configure authentication and provider settings. 

Below is a sample configuration class.

Create a new file called ``JDashConfig.cs`` and paste the below content inside.

```csharp

using JDash.NetFramework.Api;
using JDash.NetFramework.Models; 

    public class JDashConfig : BaseJDashConfigurator
    {

        public JDashConfig(JRequestWrapper request) : base(context)
        {
        }

        // Use this method to get current user for current request.
        public override JDashPrincipal GetPrincipal()
        {
            return new JDashPrincipal("user name");
        }

        // Jdash library calls this method 
        // to get a provider instance.
        public override IJDashProvider GetProvider()
        {
            // Ensure you have a valid database.

            //string msSqlConnStr = "Data Source=.;Initial Catalog=DemoJDash;Integrated Security=SSPI;";
            //return new JDash.NetFramework.Provider.MsSQL.JSQLProvider(msSqlConnStr);

            // if you are using MySql uncomment below lines.
            string mySqlConnStr = "Server=127.0.01;Database=jdash;Uid=root;Pwd=1;";
            return new JDash.NetFramework.Provider.MySQL.JMySQLProvider(mySqlConnStr);

        }
    }

```


## Step 3: Registering JDash Api endpoints
Open ``Global.asax.cs`` and paste below namespaces first. This will add extension methods.

```csharp
using JDash.NetFramework.Api; 
using JDash.NetFramework.Models;
```

Locate ``Application_Start`` method in your ``Global.asax.cs`` file and replace it as below.

```csharp
  protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);

             //this line will bind jdash routes
            JDash.NetFramework.Api.Routing.Configurator.UseJDash<JDashConfig>();

            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }

```

## Step 4: Client side development

Use ``npm`` to install JDash User Interface package. Ensure you are inside root of your project folder (where *.csproj file exists).

```no-highlight
npm install jdash-ui --save
```

Note: If this is the first time you use npm to add a package, first execute `npm init` to create ``package.json`` file.

This will create ``node_modules/jdash-ui`` folder.

Create index.html inside root folder and paste below code.


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

For demonstration purposes, we will serve a this static index.html page, to configure your routing just locate your 

``RouteConfig.RegisterRoutes`` method and replace it like below 


```csharp
  public static void RegisterRoutes(RouteCollection routes)
        {
            routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
            // this line will allow your "/" url will server the index.html page as default page.
            routes.IgnoreRoute("");

            routes.MapRoute(
                name: "Default",
                url: "{controller}/{action}/{id}",
                defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
            );
        }
```


### Step 5: Develop your first dashlet
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

### Step 6: Create and display dashboard
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

### Step 7: View dashboards
As the last step, we will implement the code to view existing dashboards.

`jdash.Provider.getMyDashboards` method returns a list of dashboards current user owns.

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
Note that the ``createDashboardList()`` function is commented on Step 6. Uncomment this function after Step 8, so that the when page loads / user creates a new dashboard, dashboard list is updated.


## Step 8: Run your application

Now run your MVC application and enjoy JDash. 

### Step 10: Download source code
Source code of this guide can be found at this [GitHub Repo](https://github.com/aibrite/jdash-netframework-tutorial).