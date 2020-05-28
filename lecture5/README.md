# Lecture 5 - JavaScript
It is useful to distinguish between code that is run by the client, the user interacting with a web application, and the server, which is the Flask application running the website. The client makes an HTTP request to the server, which is a running some Python code. The server processes the response to understand what the client is asking for, and ultimately sends back some HTML and CSS content which is rendered in the client’s browser. It is often useful, however, to have code that does run client-side. Client-side processes reduce load on the server and are often faster.

## Using JavaScript with HTML and CSS
JavaScript is a programming language designed to be run inside a web browser that is run client-side. There are many different versions of JavaScript that are supported by different browsers, but there are certain standard versions that are supported by most. In this class, one of the more popular, recent versions, ES6, will be used.

When embedded directly inside the HTML code for a webpage, it is enclosed in `<script></script>` tags.

```html
<script>
    alert('Hello, world!');
</script>
```

The previous code example, if placed in the head element, for example, would run as soon as the page is loaded. JavaScript can also be run in response to events.

```html
<html>
    <head>
        <script>
            function hello() {
                alert('Hello!');
            }
        </script>
        <title>My Website</title>
    </head>
    <body>
        <h1>Welcome!</h1>
        <button onclick="hello()">Click Here!</button>
    </body>
</html>
```

Now, the JavaScript code is contained inside a function. Note that the function is delimited by curly braces.

The function `hello` is never called inside the `script` element. Rather, there is a `button` element with the `onclick` attribute which has the `hello` function as its value. The clicking of a button is one events that JavaScript understands which can be used as a trigger. In this case, that trigger runs the `hello` function.

Some other JavaScript events include:
- `onmouseover` : triggers when an element is hovered over
- `onkeydown` : triggers when a key is pressed
- `onkeyup` : triggers when a key is released
- `onload` : triggers when a page is loaded
- `onblur` : triggers when an object loses focus (when moving away from an input box, for example)