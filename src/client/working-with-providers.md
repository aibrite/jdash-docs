# Working with Provider

## Provider Basics

JDash uses `jdash.Provider` methods to create, save and delete dashboards and dashlets.

Provider should be initialized before using any of the below methods. 

### Initializing Provider

If you are using JDash Cloud, initializing means setting a valid Api Key and User Token.

After <a href="https://app.jdash.io/#!/app/account/register" target="_blank">registering as a new user</a>, you will get an Api Key and a Test User Token. Use these data to initialize provider.

```javascript
jdash.ready(function () {
    jdash.Provider.init({
        apiKey: 'YOUR API KEY',
        userToken: 'USER TOKEN'
    })
})
```

### User Based Design 
In JDash, a dashboard belongs to a user. A user may have many dashboards and a dashboard contains dashlets.

This allows your users to create and design their own dashboards.

When you initialize provider, `apiKey` represents your application in JDash Cloud. `userToken` allows you to set a security token for the current user. Methods like `createDashboard` uses this token to assign dashboard to the current user. 

### Methods and Samples

Use `getDashboard` method to get dashboard model with dashboard id.
```javascript
jdash.Provider.getDashboard('58b72f14421b57354bf58cc5').then(function(model) { 
        console.log(model)
    }).catch(function(err) { 
        console.log(err)
    })
```
All provider methods return promises. 

Use `createDashboard` method to create a dashboard.
```javascript
jdash.Provider.createDashboard({
    title: 'My dashboard'
}).then(function (result) {
    console.log('Dashboard created with id:' + result.id);
    dashboard.load(result.id);
}).catch(function (err) {
    alert('There was an error creating dashboard: ' + err.message || err)
})
```
Note that the user, who creates the dashboard is automatically retreived from the User Token.

Use `getMyDashboards` method to get a list of dashboards, which belongs to the current user.

Use `searchDashboards` method to search for dashboards.

Use `deleteDashboard` method to delete a dashboard. When a dashboard is deleted, all dashlets inside that dashboard are also automatically deleted.

Use `saveDashboard` to update a dashboard.

Provider also has CRUD methods for dashlets. Although you can use these methods directly, it's a better practice to use the methods of `j-dashboard` element to create, remove or save dashlets.

Below table is a summary of methods for dashlets:

| Method       | Description                   |
| ------------- | -------------------------     |
| createDashlet   | Creates dashlet            |
| saveDashlet     | Updates a dashlet |
| deleteDashlet | Deletes a dashlet       |

### Key interfaces

`jdash.Provider` implements `IClientProvider` interface.

```typescript
interface IClientProvider {
    getDashboard(id: string): Promise<GetDashboardResult>;
    createDashboard(model: DashboardCreateModel): Promise<CreateResult>;
    getMyDashboards(query?: Query): Promise<QueryResult<DashboardModel>>;
    searchDashboards(search?: ISearchDashboards, query?: Query): Promise<QueryResult<DashboardModel>>;
    deleteDashboard(id: string): Promise<any>;
    saveDashboard(id: string, updateValues: DashboardUpdateModel): Promise<any>;
    createDashlet(model: DashletCreateModel): Promise<CreateResult>;
    deleteDashlet(id: string): Promise<any>;
    saveDashlet(id: string, updateValues: DashletUpdateModel): Promise<any>;
}
```
See below definitions for dashboard, dashlet and common data types.

```typescript

interface DashboardModel {
    id: string;
    title: string;
    appid: string;
    user: string;
    config: {
        [key: string]: any;
    };
    description: string;
    layout: LayoutModel;
    createdAt: Date;
    shareWith?: string;
}

interface DashletModel {
    moduleId: string;
    dashboardId: string;
    id?: string;
    title?: string;
    description?: string;
    configuration?: {
        [key: string]: any;
    };
    createdAt: Date;
}

interface ISearchDashboards {
    user: string | Array<string>;
    shareWith?: string | Array<string>;
}

interface Query {
    limit: number;
    startFrom?: any;
}

interface SearchQuery extends Query {
    filters: any;
}

interface QueryResult<T> {
    data: Array<T>;
    hasMore: boolean;
}

interface CreateResult {
    id: string;
}

interface GetDashboardResult {
    dashboard: DashboardModel;
    dashlets: Array<DashletModel>;
}

interface DashboardCreateModel {
    id?: string;
    title?: string;
    description?: string;
    shareWith?: string;
    layout: LayoutModel;
    config?: {
        [key: string]: any;
    };
    user?: string;
}

interface DashboardUpdateModel {
    title?: string;
    description?: string;
    shareWith?: string;
    layout?: LayoutModel;
    config?: {
        [key: string]: any;
    };
}



interface DashletPositionModel {
    zone?: string;
    y?: number;
    x?: number;
    z?: number;
}

interface DashletCreateModel {
    moduleId: string;
    dashboardId: string;
    id?: string;
    title?: string;
    description?: string;
    configuration?: {
        [key: string]: any;
    };
}

interface DashletUpdateModel {
    title?: string;
    description?: string;
    configuration?: {
        [key: string]: any;
    };
}

interface LayoutDashletMetaModel {
    data?: any;
    position?: DashletPositionModel;
}

interface LayoutModel {
    moduleId: string;
    config?: {
        [key: string]: any;
    };
    dashlets?: {
        [key: string]: LayoutDashletMetaModel;
    };
}

```
