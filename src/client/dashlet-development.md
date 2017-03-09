# Dashlet Development

## About dashlets
In Jdash, *Dashlet* means a custom element which consists of Html dom elements and optional business layer.

Jdash includes component based custom element development framework. This framework allows you to develop and register custom elements easily.

## Defining dashlets

```html
<!-- Define <hello-world> element -->
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

`template`element is used to set html dom elements. When a new instance of dashlet is created, elements inside template element is cloned and dom of new dashlet instance is created.

`script` element is optional and allows you to execute javascript for dashlet. 

After defining a dashlet you can add it to a dashboard or just use as custom element.

```html
<hello-world></hello-word>
```
or add to a dashboard.

```javascript
dashboard.addDashlet('hello-world');
```
Jdash also supports placing dashlet definition inside another file.

```html
<link rel="import" href="dashlets/helloworld.html">
```

It is also possible to seperate dashlet dom and script. Below is an example.

```html
<!-- put a template element with an id value same as id of your dashlet -->
<template id="hello-world">
    <h1></h1>
</template>

<j-dashlet id="hello-world" title="Hello world!">
</j-dashlet>

<script>

// Define hello-world dashlet
jdash.define('hello-world', function () {

    this.initialized = function () {
        // Dom is ready!
        var h1 = this.querySelector('h1')
        h1.textContent = 'Hello World!';
    }

})
</script>
```

## Dashlet lifecycle

Since Jdash uses custom elements as dashlets same rules for custom elements apply for dashlets.

Below code summarizes dashlet lifecycle.

```javascript
jdash.define('hello-world', function () {

    // a new instance is created.

    this.initialized = function () {
        // Dom is ready!
    }

    this.connected = function() {
        // Attached to document
    }

    this.disconnected = function() {
        // Detached from document
    }

})
```

See <a href=" https://www.w3.org/TR/custom-elements/" target="_blank">W3C Custom Elements</a> document to get more information about custom elements.

Note: For backward compability we used ES5 style dashlet definition in this document. Jdash also supports ES6 and you can use `class` keyword and inherit from `Dashlet` to define your dashlet.

## Configurable dashlets

