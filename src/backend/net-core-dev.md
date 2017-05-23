# .Net Core Development

This article is about installing and using JDash server libraries on your backend. If you decide to use JDash Cloud, you can skip this section.

Integrating JDash server libraties into your existing .Net Core Web Api/MVC application is easy.


## Step 1 : Add JDash.NetCore References To Your Project

You need to add 2 basic references to your application to get JDash going

    1- JDash.NetCore.Api
    2- JDash.NetCore.Models

For persistance you can either use our own references for MySQL/MsSql or you can implement it yourself via using interface below:

    JDash.NetCore.Models.IJDashPersistenceProvider
     
    TODO : How to include? Where to get dlls? Is it source code? Is it a package?

## Step 2 : Implementing JDash.NetCore To Your Application

We made JDash pretty easy and configurable so that you can implement it easily. Mosft of your work done via javascript and this .Net Core libraries are used for persistance/authorization layer only.

On your Startup.cs file :

  // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
 
```c#
        //add below usings above code to your code
        using JDash.NetCore.Api; 
        using JDash.NetCore.Models;
```

```c#
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


    public class JDashConfigurator : BaseJDashConfigurator
    { 
        public JDashConfigurator(HttpContext context, bool ensureTablesCreated)
         : base(context, ensureTablesCreated)
        {
            this._ensureTablesCreated = true; // defaults to true.. It will check whether tables are created,
                                             // if not creates them. 
            // You can disable this feature by setting this property false;                                             
        }


        public override JDashPrincipalResult GetJDashPrincipal(string authorizationHeader)
        {
            var context = this.HttpContext; // gets current context for http request of JDash.
            // You can use authorization header to check current user (see token authorization for JDash). If you are using cookies authentication, you can simply get username from 
            
            // this.HttpContext.User.Identity.Name

            // appid is for multiple applications, if you have only 1 application, you can just write your application name there (appid must be filled).
            return new JDashPrincipalResult() { appid = "application name", user = "current user name must be determined here" };
        }

        public override IJDashPersistenceProvider GetPersistanceProvider()
        {
            // Here we are using MsSqlProvider for JDash from package 
            // JDash.NetCore.Provider.MsSQL.JSQLProvider

            var provider = new JSQLProvider(connectionString: "some connection string");
            return provider;
        }
    }

```


### Using providers

Currently JDash supports MSSQL an MySQL to store and retrieve dashboard data.

You can use the ``` BaseJDashConfigurator.GetPersistanceProvider() ``` method to determine the persistance provider you will use as the implementation details shown at "Step 2: Implementing JDash.NetCore To Your Application"

You can use these built-in packages as reference for your application :

    1- JDash.NetCore.Provider.MsSQL 
    2- JDash.NetCore.Provider.MySQL 


If you want another data provider, you can also implement it yourself by using 

```c# 
        JDash.NetCore.Models.IJDashPersistenceProvider
```

Interface so that you can easily integrate your own provider yourself.