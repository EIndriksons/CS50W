# Lecture 7 - Django
Django is a much heavier weight web framework than Flask with a lot more out-of-the-box features that would’ve had to be built up manually and repetitively with a micro-framework like Flask.

## Using Django
Django divides all of its web applications into **projects**, composed of different parts. To start a new project, run `django-admin startproject projectname`.

### Project Components
Django creates a number of files with a new project:
- `__init__.py` : defines the directory `projectname` as a Python **package**, a collection of multiple Python files. Django is built on the idea of packages. A web application can be made up of multiple packages, each serving a slightly different purpose, and Django will help manage these.
- `views.py` : analogous to `application.py` for a Flask application.
- `manage.py` : a Python script that can be used to perform useful operations on a web application.
- `settings.py` : basic settings, like time zone, other applications installed in the project, what sort of database is used, etc.
- `urls.py` : determines what URLs/routes can be accessed when using the web application
- `wsgi.py` : a file that helps to deploy an application to a web server
- `project_name/` : the directory for the project that contains all of the above files by default

A Django project consists of one or more Django applications, or apps, which serves a particular purpose.

## A Basic Application
To create an app, inside the project directory, run `python manage.py startapp appname`. This will create a directory `appname` inside of the project directory. `appname` will contain a number of files automatically.

Inside of `appname`, `views.py` is analogous to `application.py` for a Flask application. It contains the code that determines what the user sees at a particular route. At first, it will look like this:

```py
from django.shortcuts import render

# Create your views here.
```

All view functions should take the `request` object as an argument. Like in Flask, this object will contain information about what sort of arguments were passed in to the request, etc. A baisc view might just return an simple HTTP response.

```py
from django.http import HttpResponse
from django.shortcuts import render

# Create your views here.
def index(request):
    return HttpResponse("Hello, world!")
```

That basic view, however, does not specify what route it is at. To do so, a new `urls.py` must be created inside the `appname` directory (this is a different `urls.py` than the project-level file of the same name). Each application will often have its own routes, and these separate `urls.py` help to signal that difference in functionality, keep things organized, and make apps easily reusable in other projects. `appname/urls.py` could look like this:

```py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index),
]
```

`from . import views` imports `views.py` from the `appname` directory, so that URLs can be linked to views.

`urlpatterns` is a list of all the URLs supported by this application.

`""` indicates the empty route.

When the Django project starts up, it will only check the `urls.py` at the project level. So, the final step before this basic application is actually usable, `appname/urls.py` must be linked to the project’s `urls.py`, which starts off with some code in it already.

```py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls)
]
```

To link the new path, simply add the path to `urlpatterns`:

```py
urlpatterns = [
    path("", include("appname.urls")),
    path("admin/", admin.site.urls)
]
```

The reason for this apparent complexity is to allow for routing amongst multiple different applications. This `urls.py` serves as the dispatch point for all those lower-level `urls.py` files.

To run the application, run `python manage.py runserver`.