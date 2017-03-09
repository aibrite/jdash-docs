# Working with Provider

## Provider basics

Jdash uses `jdash.Provider` methods to create, save and delete dashboards and dashlets.

Provider should be initialized before using any methods of the below methods. 

### İnitializing provider

If you are using Jdash Cloud initializing means setting a valid api key and user token.

After <a href="https://app.jdash.io/#!/app/account/register" target="_blank">registering as a new user</a> you will get an api key and test user token. Use them to initialize provider.

```javascript
jdash.ready(function () {
    jdash.Provider.init({
        apiKey: 'YOUR API KEY',
        userToken: 'USER TOKEN'
    })
})
```

### User based design 
In Jdash, a dashboard belongs to a user. A user may have many dashboards and a dashboard contains dashlets.

This allows your users to create and design their own dashboards.

When you initialize provider, `apiKey` represents your application in Jdash cloud. `userToken` allows you to set a security token for current user. Methods like `createDashboard` uses this token to assign dashboard to current user. 

### Methods and samples

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
Note that the user which creates the dashboard is autoımatically retreived from the user token.

Use `getMyDashboards` method to get a list of dashboards which belongs to current user.

Use `searchDashboards`method to search for dashboards.

Use `deleteDashboard` method to delete a dashboard. When a dashboard is deleted all dashlets inside that dashboard are automatically deleted.

Use `saveDashboard` to update a dashboard.

Provider also has CRUD methods for dashlets. Although you can use these methods directly it's better to use methods of `j-dashboard` element to create, remove or save dashlets.

Below table is a summary of methods for dashlets.

| Method       | Description                   |
| ------------- | -------------------------     |
| createDashlet   | Creates dashlet            |
| saveDashlet     | Updates a dashlet |
| deleteDashlet | Deletes a dashlet       |

### Key interfaces

`jdash.Provider` implements `IClientProvider`interface.

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
