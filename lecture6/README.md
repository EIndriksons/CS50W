# Lecture 6 - Front Ends

## Single Page Apps
Single-page apps take content that would ordinarily be on multiple different pages (or routes) and combine them into a single page that pulls new information from the server whenever it’s needed (through methods such as AJAX).

For a starting point, this application uses multiple pages.

```py
@app.route("/")
def first():
    return render_template("first.html")

@app.route("/second")
def second():
    return render_template("second.html")

@app.route("/third")
def third():
    return render_template("third.html")
```

Here’s the layout template for these pages.

```html
<html>
    <head>
        <title>My Webpage</title>
    </head>
    <body>
        <ul id="nav">
            <li><a href="">First Page</a></li>
            <li><a href="">Second Page</a></li>
            <li><a href="">Third Page</a></li>
        </ul>
        <hr>
        
        {% block body %}
        {% endblock %}
        
    </body>
</html>
```

The navigation bar is simply an unordered list of links.

Given that these pages all have the simple function of displaying text, `application.py` can be reworked to run on a single route.

```py
@app.route("/")
def index():
    return render_template("index.html")

texts = ["text 1", "text 2", "text 3"]

@app.route("/first")
def first():
    return texts[0]

@app.route("/second")
def second():
    return texts[1]

@app.route("/third")
def third():
    return texts[2]
```

Note that the other **routes** don’t return a new webpage, but rather just the text that should be displayed.

In order to process this structure, JavaScript must be added to `index.html`.

```html
<html>
    <head>
        <script>
            document.addEventListener('DOMContentLoaded', () => {

                // Start by loading first page.
                load_page('first');

                // Set links up to load new pages.
                document.querySelectorAll('.nav-link').forEach(link => {
                    link.onclick = () => {
                        load_page(link.dataset.page);
                        return false;
                    };
                });
            });

            // Renders contents of new page in main view.
            function load_page(name) {
                const request = new XMLHttpRequest();
                request.open('GET', `/${name}`);
                request.onload = () => {
                    const response = request.responseText;
                    document.querySelector('#body').innerHTML = response;
                };
                request.send();
            }
        </script>
    </head>
    <body>
        <ul id="nav">
            <li><a href="" class="nav-link" data-page="first">First Page</a></li>
            <li><a href="" class="nav-link" data-page="second">Second Page</a></li>
            <li><a href="" class="nav-link" data-page="third">Third Page</a></li>
        </ul>
        <hr>
        <div id="body">
        </div>
    </body>
</html>
```

`load_page` makes an AJAX request to the server to get the text that should be displayed and puts in the `body` `div`.

This new single-page implementation avoids reloading the page repeatedly just to display very similar content (e.g. the same navigation bar). However, this eliminates the URL’s functionality as a locator, because all the content is on the same route.

## HTML5 History API
The HTMuL5 History API allows for the manipulation of a browser’s history and URL even if the page is still being implemented with a single-page design. Whenever an new **page** is accessed, the client can **push** a new URL state.

The changes to the JavaScript code are inside the load_page function.

```js
function load_page(name) {
    const request = new XMLHttpRequest();
    request.open('GET', `/${name}`);
    request.onload = () => {
        const response = request.responseText;
        document.querySelector('#body').innerHTML = response;

        // Push state to URL.
        document.title = name;
        history.pushState(null, name, name);
    };
    request.send();
}
```

`document.title` is just an aesthetic property that is set to reflect the current page.

In the `history.pushState()` function, which is used to change the browser’s history, the first argument is any data that should be associated with the push, the second argument is the title of the page being pushed, and the third argument is the URL being pushed.

One flaw with this, though, is that the full multi-page behavior is not truly being emulated. If a user tries to use the back button in their browser, the URL will change, but not the content. To remedy this, the full, stack-like behavior of the HTML5 History API can be used. Going back in history should just **pop** whatever the URL is on top off of the stack.

```js
// Renders contents of new page in main view.
function load_page(name) {
    const request = new XMLHttpRequest();
    request.open('GET', `/${name}`);
    request.onload = () => {
        const response = request.responseText;
        document.querySelector('#body').innerHTML = response;

        // Push state to URL.
        document.title = name;
        history.pushState({'title': name, 'text': response}, name, name);
    };
    request.send();
}

// Update text on popping state.
window.onpopstate = e => {
    const data = e.state;
    document.title = data.title;
    document.querySelector('#body').innerHTML = data.text;
};
```

Now, when pushing a new state, title and text data is being pushed with it.

When the state is popped, `e`, the event that just took place, has a `state` property that contains all the data that was pushed with that state. Then, that data is just used to update the contents of the page as expected.

## Window and Document
The `window` and `document` variables, which have been seen in past examples, are just examples of JavaScripts objects on which operations can be performed and that have properties that can be accessed. In particular, they contain information about their size and position.

- `window.innerWidth` : window width
- `window.innerHeight` : window height
- `document.body.offsetHeight` : the entire height of the HTML body’s document, of which the window height is likely just a small porition
- `window.scrollY` : how far down the page has been scrolled (in pixels)

One potential use of these properties is to be able to detect if the user has scrolled to the bottom of the page.

```js
window.onscroll = () => {
    console.log('----');
    console.log(window.innerHeight);
    console.log(window.scrollY);
    console.log(document.body.offsetHeight);
    if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
        document.querySelector('body').style.background = 'green';
    } else {
        document.querySelector('body').style.background = 'white';
    }
};
```

`console.log` is essentially a print statement that prints to the web browsers’s console.

All this does is change the background color of the web page to green when the bottom of the document has been reached, which is detected using the mathematical relationship between `window` and `document` properties.

A more useful application of this bottom-detection would be the dynamic loading of more content when the bottom of a webpage has been reached. `application.py` for such a webpage could look like this.

```py
import time

from flask import Flask, jsonify, render_template, request

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/posts", methods=["POST"])
def posts():

    # Get start and end point for posts to generate.
    start = int(request.form.get("start") or 0)
    end = int(request.form.get("end") or (start + 9))

    # Generate list of posts.
    data = []
    for i in range(start, end + 1):
        data.append(f"Post #{i}")

    # Artificially delay speed of response.
    time.sleep(1)

    # Return list of posts.
    return jsonify(data)
```

`index.html` (a little more complex now):

```html
<html>
    <head>
        <script>
            // Start with first post.
            let counter = 1;

            // Load posts 20 at a time.
            const quantity = 20;

            // When DOM loads, render the first 20 posts.
            document.addEventListener('DOMContentLoaded', load);

            // If scrolled to bottom, load the next 20 posts.
            window.onscroll = () => {
                if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
                    load();
                }
            };

            // Load next set of posts.
            function load() {

                // Set start and end post numbers, and update counter.
                const start = counter;
                const end = start + quantity - 1;
                counter = end + 1;

                // Open new request to get new posts.
                const request = new XMLHttpRequest();
                request.open('POST', '/posts');
                request.onload = () => {
                    const data = JSON.parse(request.responseText);
                    data.forEach(add_post);
                };

                // Add start and end points to request data.
                const data = new FormData();
                data.append('start', start);
                data.append('end', end);

                // Send request.
                request.send(data);
            };

            // Add a new post with given contents to DOM.
            function add_post(contents) {

                // Create new post.
                const post = document.createElement('div');
                post.className = 'post';
                post.innerHTML = contents;

                // Add post to DOM.
                document.querySelector('#posts').append(post);
            };
        </script>
    </head>
    <body>
        <div id="posts">
        </div>
    </body>
</html>
```

For a little more functionality, the `add_post` function could be modified to add a button to hide uninteresting posts.

```js
function add_post(contents) {

    // Create new post.
    const post = document.createElement('div');
    post.className = 'post';
    post.innerHTML = contents;

    // Add button to hide post.
    const hide = document.createElement('button');
    hide.className = 'hide';
    hide.innerHTML = 'Hide';
    post.append(hide);

    // When hide button is clicked, remove post.
    hide.onclick = function() {
        this.parentElement.remove();
    };

    // Add post to DOM.
    document.querySelector('#posts').append(post);
};
```

Calling `post.append(hide)` adds the hide button inside the post `div`.

`parentElement` is the element containing the element in question. In this case, `this.parentElement` is used to refer to the `post` containing the `hide` button.

`remove` is a built-in function to delete an element all together.