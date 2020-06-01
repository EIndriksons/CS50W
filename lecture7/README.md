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