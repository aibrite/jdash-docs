# Basic Concepts

Using JDash you can add drag & drop, end-user designable dashboards to your application.

## Features

### Licensing Models

There are two licensing models.

**JDash Cloud**

You can use [JDash Cloud](https://app.jdash.io) to store dashboard & dashlet related data. 

If you have a cloud based application and don't want to integrate JDash backend platform into your server application, this is an ideal, cheap and fast solution to get you up.

**On Premise**

If you are a developer/company, who develops applications for other customers or if you have a product, into which you want to embed JDash, this is the right license type for you.

You can integrate JDash backend platform into your server application easily.

* If you are using NodeJs, we support it with MongoDB and MySQL.
* If you are using Asp.Net MVC 5+, we support it with MySQL and MSSQL.
* If you are using .Net Core, we support it with MySQL and MSSQL.

If you need to implement other platforms/database servers, just drop us an e-mail (hello@jdash.io).

### Custom elements
JDash uses W3C custom elements spec for dashlet development. 

On top of this spec, we developed a component framework to make dashlet development easier.

Here is a sample dashlet.

```html
<j-dashlet id="hello-world" title="Hello world!">
    <template>
        <!-- This will be the HTML content of your dashlet  -->
        <!-- Think template as user interface layer of your dashlet -->
        <h1></h1>
    </template>
    <script>

        // An optional javascript function allows you implement
        // your business logic.
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
Above code snippet defines `hello-world` custom element, which inherits from `jdash.Dashlet` class.

You can also use this element directly inside your HTML.

```html
<hello-world></hello-world>
```

### Key elements

Below table shows key elements and descriptions.

| Element       | Description                   |
| ------------- | -------------------------     |
| j-dashboard   | Displays dashboard            |
| j-dashlet     | Defines a new dashlet element |
| j-grid-layout | Multi column/row layout       |


Dashboard element contains one layout element and many dashlets inside that layout element. Layout is responsible for managing drag-drop, resizing and saving/loading dashlet positions.

It is also possible to develop new layout elements by inheriting `jdash.DashboardLayout` class. Currently JDash framework contains one layout element: `j-grid-layout`.

