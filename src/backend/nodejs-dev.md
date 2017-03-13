# NodeJS Development

You can implement JDash server sided API for yourself on NodeJS server. We have npm packages for your needs. It is easy to implement and run JDash API Server on your machines instead of JDash Cloud.

## Step 1: Install JDash-Express
Use npm to install Jdash express library.
 
    npm install jdash-express --save

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
    // configura jexpress with router and user callback
        jexpress({
            principal: function (request) {
                return {
                    user: request.user.id, // user identifier you must give this value unique per user
                    appid: request.app_id.toString() // if you have multiple applications you must give this value , otherwise just write your own application name.
                }
            },
            provider: null // we will explain provider details below.
        }).use(jdashRoutes);
```

Above code sample will simply attach jexpress api routes for your application.

## Persistent Data Providers - jdash-mongodb and jdash-mysql

JExpress supports persistent provider classes with interfaces below :

```typescript
export interface ISearchDashboard {
    appid?: string | Array<string>;
    user?: string | Array<string>;
    shareWith?: string | Array<string>;
}

export interface ISearchDashlet {
    user?: string | Array<string>;
    dashboardId?: string;
}

export interface IDBProvider {
    getDashboard?(appid: string, id: string): Promise<GetDashboardResult>;
    searchDashboards(search: ISearchDashboard, query?: Query): Promise<QueryResult<DashboardModel>>;
    createDashboard(model: DashboardModel): Promise<CreateResult>;
    deleteDashboard(appid: string, id: string): Promise<any>;
    updateDashboard(appid: string, id: string, updateValues: DashboardUpdateModel): Promise<any>;

    createDashlet(model: DashletModel): Promise<CreateResult>;
    searchDashlets(search: ISearchDashlet): Promise<Array<DashletModel>>;
    deleteDashlet(id: string | Array<string>): Promise<any>;
    updateDashlet(id: string, updateValues: DashletUpdateModel): Promise<any>;

}
```

You can implement your own classes or use our pre-defined providers jdash-mongodb or jdash-mysql


### Implementing jdash-mongodb to your jdash-express package

We wrote `Step 2: Attaching jdash-express middleware to your application` code for jdash-express. There was a property we passed null "provider". This property is for persistant data providers.

Implementing jdash-mongodb is really easy 

    JDash-MongoDB

/// installation part for jdash-mongodb

```typescript
          var mongoose = require('mongoose');
          var jmongo = require('jdash-mongodb').default;

          var connStr = ...
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

All you have to do for jdash-mongodb is this, now you can use jdash-express and jdash-mongodb without any problems.

    JDash-MySQL

/// installation part for jdash-mysql

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

All you have to do for jdash-mysqldb is this, now you can use jdash-express and jdash-mongodb without any problems.