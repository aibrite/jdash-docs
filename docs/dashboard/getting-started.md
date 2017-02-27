# Getting Started

## Quick Start
The aim of this article is to get you up and running with jdash in 5 minutes.
### Step 1: Get a free api key
JDash cloud allows you to save and retrieve user data i.e. dashboards, layouts, dashlet configurations and positions.

To get a free api key use <a href="https://app.jdash.io" target="_blank">JDash Cloud</a>

### Step 2: Install JDash Client
```no-highlight
bower install jdash
```
### Step 3: Create a basic HTML page

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>A Simple Page with CKEditor</title>
        <!-- Make sure the path to CKEditor is correct. -->
        <script src="../ckeditor.js"></script>
    </head>
    <body>
        <form>
            <textarea name="editor1" id="editor1" rows="10" cols="80">
                This is my textarea to be replaced with CKEditor.
            </textarea>
            <script>
                // Replace the <textarea id="editor1"> with a CKEditor
                // instance, using default configuration.
                CKEDITOR.replace( 'editor1' );
            </script>
        </form>
    </body>
</html>
```

### Step 4: Develop your first dashlet

## Basic Concepts
