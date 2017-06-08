# Dashlet Development

## About Dashlets
In JDash, *Dashlet* means a custom element which consists of Html dom elements and optional business layer.

JDash includes component based custom element development framework. This framework allows you to develop and register custom elements easily.

## Defining Dashlets

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

`script` element is optional and allows you to execute Javascript for dashlet. 

After defining a dashlet, you can add it to a dashboard or just use it as a custom element.

```html
<hello-world></hello-word>
```
or add to a dashboard.

```javascript
dashboard.addDashlet('hello-world');
```
JDash also supports placing dashlet definitions inside another file.

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

## Dashlet Lifecycle

Since JDash uses custom elements as dashlets, same rules for custom elements apply for dashlets.

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

Note: For backward compability, we used ES5 style dashlet definition in this document. JDash also supports ES6 and you can use `class` keyword and inherit from `Dashlet` to define your dashlet.

## Configurable Dashlets

### Defining Editor Element

Use `j-dashlet-editor` element to define a dashlet editor. Same rules (templates, script, lifecycle etc.) are valid for dashlet editors since they are developed with JDash component framework.

```html
<j-dashlet-editor id="hello-world-editor">

    <template>
        Editor content goes here
    </template>

    <script>
        jdash.define(function () {

            this.initialized = function () {
                // Dom is ready!
            }

        })
    </script>

</j-dashlet-editor>
```
When a new dashlet is defined, JDash looks for an element tag that is postfixed by `-editor`. For example if your dashlet is defined by `hello-world` JDash looks for `hello-world-editor`. If it finds such element, it associates this tag as editor for your dashlet.

You can override this behaviour by explicitly setting a value to `j-editor` attribute.

```html
<j-dashlet id="hello-world" j-editor="my-editor" title="Hello world!"></j-dashlet>
<j-dashlet-editor id="my-editor"></j-dashlet-editor>
```
### Loading Configuration
You can set and retrieve configuration values for dashlets. Configuration values are automatically converted to Json and stored on JDash Cloud or on your backend.

```html
<j-dashlet id="hello-world" title="Hello world!">
    <template>
        <h1></h1>
    </template>
    <script>
        jdash.define(function () {

            this.loadConfig = function () {
                var h1 = this.querySelector('h1')
                h1.textContent = this.config.get('header') || 'Hello World!';
            }

            this.initialized = function () {
                // Dom is ready!
            }
        })
    </script>
</j-dashlet>
```
If you attach a function named `loadConfig` to your dashlet, JDash automatically calls it when a new instance of your dashlet is created or after successfully saving new configuration values.

```html
<j-dashlet-editor id="hello-world-editor">

    <template>
        <h3>Set a header value:</h3>
        <input type="text">
    </template>

    <script>
        jdash.define(function () {

            this.initialized = function () {
                var inputEl = this.querySelector('input');
                inputEl.value = this.dashlet.config.get('header') || '';
            }

            this.addEventListener('setconfig', function (event) {
                var inputEl = this.querySelector('input'), value = inputEl.value;
                if (!value) {
                    alert('Set a valid value please!');
                    event.preventDefault();
                } else this.dashlet.config.set('header', value)
            })

        })
    </script>

</j-dashlet-editor>
``` 
Add an event listener for `setconfig` event, so that you can set new values for configuration.

