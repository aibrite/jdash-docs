# Getting Started

## Quick Start
The aim of this article is to get you up and running with jdash in 5 minutes.

### Step 1: Get a free api key
JDash cloud allows you to save and retrieve user data i.e. dashboards, layouts, dashlet configurations and positions.

To get a free api key use <a href="https://app.jdash.io/#!/app/account/register" target="_blank">JDash Cloud</a>

### Step 2: Install JDash Client
Use npm to install Jdash client library.

```no-highlight
npm install jdash-ui --save
```

This will create node_modules/jdash-ui folder.

Note: If this is the first time you use npm to add a package first execute npm init to create a package.json.

### Step 3: Create a basic HTML page
Create index.html and paste below code inside.

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

<body>

</body>

</html>
```

### Step 4: Develop your first dashlet
Copy the following code inside body tag.

```html
    <!-- Use j-dashlet to define a dashlet -->
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
### Step 5: Create a dashboard


## Basic Concepts
