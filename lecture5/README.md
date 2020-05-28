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

## Manipulating the DOM
Beyond just displaying alerts, JavaScript has the power to actually change the contents of a webpage.

```html
<html>
    <head>
        <script>
            // Function to change heading to say goodbye
            function hello() {
                document.querySelector('h1').innerHTML = 'Goodbye!';
            }
        </script>
    </head>
    <body>
        <h1>Welcome!</h1>
        <button onclick="hello()">Click Here!</button>
    </body>
</html>
```

`document` refers to the web page currently being displayed.

`querySelector('tag')` is a function that searches through the webpage for a particular CSS selector and returns that element. If there are multiple results, only the first result is returned.

This function can also be called as `document.querySelector('#id')` and `document.querySelector('.class')`. More sophisticated selectors, selecting only descendants of certain element,s for example, can also be used.

The `innerHTML` property of an element is the HTML content contained within the element’s tags.

When the `button` is clicked, the text `Welcome!` changes to `Goodbye!`.


This slightly more advanced example showcases the use of variables in JavaScript.

```html
<html>
    <head>
        <script>
            let counter = 0;

            function count() {
                counter++;
                document.querySelector('#counter').innerHTML = counter;
            }
        </script>
    </head>
    <body>
        <h1 id="counter">0</h1>
        <button onclick="count()">Click Here!</button>
    </body>
</html>
```

`let` is a keyword used to define variables.
`counter++` is a shorthand to increment `counter` by 1.

Conditional statements in JavaScript look like this:

```html
<script>
    let counter = 0;

    function count() {
        counter++;
        document.querySelector('#counter').innerHTML = counter;

        if (counter % 10 === 0) {
            alert(`Counter is at ${counter}!`);
        }
    }
</script>
```

`%` is the modulus operator, which returns the remainder of the first number divided by the second.

`===` checks for exact equality in JavaScript; two things must be identical for it to return true.

The argument to `alert` is a template literal, which is like a Python format string. `${counter}` is replaced with whatever the value of the variable `counter` is. Backticks are used to delimit a template literal.

JavaScript can also be factored out of the HTML code entirely.

```html
<html>
    <head>
        <script>
            document.addEventListener('DOMContentLoaded', function() {
                document.querySelector('button').onclick = count;
            });

            let counter = 0;

            function count() {
                counter++;
                document.querySelector('#counter').innerHTML = counter;

                if (counter % 10 === 0) {
                    alert(`Counter is at ${counter}!`);
                }
            }
        </script>
    </head>
    <body>
        <h1 id="counter">0</h1>
        <button>Click Here!</button>
    </body>
</html>
```

Note that there is no `onclick` attribute in the HMTL tags for the `button` element. Nonetheless, the function `addEventListener`, which, likes it name suggests, **listens**, or waits, until the event `DOMContentLoaded` occurs. This event occurs when the entire HTML structure is loaded by the browser. Then, the second argument, a function, is called.

JavaScript makes use of **higher order functions**, which means functions can be passed around like any other value. The function being passed is called a **callback function**. The callback is called when the event being listened for occurs. In this case, that callback sets the `onclick` property of the `button` element to the `count` function, ultimately resulting in the same functionality as before.

Going one step further, the JavaScript code can be factored out of the `.html` file entirely into a separate `.js` file. Everything that’s inside the `script` element from the last example would go into the `.js` file, and the `.html` would look like this:

```html
<html>
    <head>
        <script src="counter3.js"></script>
    </head>
    <body>
        <h1 id="counter">0</h1>
        <button>Click Here!</button>
    </body>
</html>
```

This is exactly the same paradigm that was seen in factoring out CSS.

## Variables
There are three main keywords used to define variables in JavaScript.
- `const` : defines a constant variable that cannot be redefined later
- `let` : defines a variable is local to the scope of the innermost pair of curly braces surrounding it
- `var` : defines a variable that is local to the function it is defined in

Here is an example showcasing these different ways to define variables:

```html
<script>
    // This variable exists even outside the loop
    if (true) {
        var message = 'Hello!';
    }

    alert(message);
</script>
```

Because `var` was used to define `message`, there will be no errors running this code.

```html
<script>
// This variable does not exist outside the loop
if (true) {
    let message = 'Hello!';
}

alert(message);
</script>
```

Because `let` was used to define `message`, it cannot be passed to `alert`, which is outside the scope of `message`. If this were in an HTML page, when the page was opened, no alert would pop up. If the console were opened in the browser, there would be an `Uncaught ReferenceError`.

```html
<script>
// The value of const variables cannot change
const message = 'Hello!';
message = 'Goodbye!';

alert(message);
</script>
```

Similar to the last example, no alert will pop up. In the console, there would be an `Uncaught TypeError`, since there was an attempt to redefine a variable defined with `const`.


The JavaScript console, accessable in the **Develop** or **Inspect** menu in a web browser, allows for the interactive entry of JavaScript, similiar to the Python console.

Here’s another example which uses JavaScript to read info from a form.

```html
<html>
    <head>
        <script>
            document.addEventListener('DOMContentLoaded', function() {
                document.querySelector('#form').onsubmit = function() {
                    const name = document.querySelector('#name').value;
                    alert(`Hello ${name}!`);
                };
            });
        </script>
    </head>
    <body>
        <form id="form">
            <input id="name" autocomplete="off" autofocus placeholder="Name" type="text">
            <input type="submit">
        </form>
    </body>
</html>
```

The callback function here selects the element with the id `form` and sets its `onsubmit` (another event) property to another callback function which sets the `const` variable `name` to the `value` property returned from the elemnt with id `name`. `name` is the input box of a form, so `value` will be whatever the user has entered into the form.

So, this code produces an alert that says hello to whatever name the user entered into the form.