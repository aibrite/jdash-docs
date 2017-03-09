# Working with Dashboards

## j-dashboard element
Use `j-dashboard`element to display an existing dashboard.

```html
<j-dashboard id="dashboard">
    <h2 j-bind="title"></h2>
</j-dashboard>
```

## Viewing dashboard
Use `load` method to load (display) a dashboard.

```typescript
load(id: string | DashboardModel): Promise<DashboardModel>;
```

`load`method returns a promise and you can use dashboard id or `DashboardModel`.

As a common scenario you can use jdash.Provider.getMyDashboards method to get a list of dashboard for current user. After a click for a dashboard use `load`method to display selected dashboard.

Refer to Getting Started for sample usage of `load`method.

### About View modes
A dashboard may have one of the following view modes.

| View mode       | Description                   |
| -------------   | -------------------------     |
| readonly        | Displays dashboard in readonly mode. Best for viewing dashboard            |
| dashletedit     | User can edit dashlet properties and positions. Best for creating and configuring dashlets |
| layoutedit      | User can edit layout of dashboard. Best for desiging structure of dashboard.       |

Set initialize dashboard view mode use `j-view-mode` attribute of `j-dashboard` element. 

```html
<j-dashboard id="dashboard" j-view-mode="dashletedit">

</j-dashboard>
```

You can also use HTMLElement.setAttribute method to change it later.
```javascript
var dashboard = document.querySelector('#dashboard');
dashboard.setAttribute('j-view-mode', 'readonly');
```

## Adding dashlets

### Getting a list of available dashlets
Use jdash.DashletModule.getModules method to get a list of available dashlet elements. Refer to Dashlet Development (next section) for more information about defining dashlets.

You can use `title`attribute to display a dashlet to user. `id` attribute can be used to create an instance.

```javascript
var dashletElements = jdash.DashletModule.getModules();
for(var i = 0; i < dashletElements.length;i++) 
    console.log(dashletElements[i].title, ',', dashletElements[i].id)
```
An example output can be
```no-highlight
Hello World, hello-world
Astronomy of Day, nasa-apod-dashlet
Rss Dashlet, rss-dashlet
Chart Dashlet, chart-dashlet
Pure HTML, content-dashlet
Rich Content, rich-content
```

### Adding dashlet to dashboard

Use `addDashlet` method of `j-dashboard`element to create a new dashlet.

```javascript
dashboard.addDashlet('hello-world');
```

You can also create a dashlet instance and use that instance instead of using element tag.
```javascript
var mydashlet = document.createElement('hello-word');
mydashlet.title = 'Say Hi to me!';
dashboard.addDashlet(mydashlet);
```
`j-dashboard`element automatically calls `createDashlet`method of provider so new dashlet is persisted and will be available when dashboard loads at later.

### Drag-drop adding
As an alternative you can use makeDroppable method of layout. This lets yo to create an HTML element for a dashlet and users can add this dashlet just by dropping to the dashboard element.

Refer to sample applications to see how to use `makeDroppable` method. 