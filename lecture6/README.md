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