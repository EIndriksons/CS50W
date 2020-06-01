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

## Flights Revisited
To demonstrate Django more completely, the next example will reconstruct and expand upon the flight manager application that was originally built with Flask. The project name will be `djangoair`, and it will contain an application called `flights`.

To start off, `flights/urls.py`:

```py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index),
]
```

These urls should be linked to djangoair/urls.py in the same way as the previous example.

`flights/views.py`:

```py
from django.http import HttpResponse
from django.shortcuts import render

# Create your views here
def index(request):
    return HttpResponse("Flights")
```

This application is now at the same point as the previous example. The next step is to add the database. Django was designed for interacting with data, so it makes it very easy to do so. `flights/models.py` looks like this right now:

```py
from django.db import models

# Create your models here
```

This the file to define the classes which will define the types of data being stored in the database. The information to be contained here is very analagous to the information created with Flask-SQLAlchemy.

A model for a flight might look like this:

```py
class Flight(models.Model):
    origin = models.CharField(max_length=64)
    destination = models.CharField(max_length=64)
    duration = models.IntegerField()
```

Inherting `models.Model` just establishes this class as a Django model.

Django has a number of built-in types of fields that map to different types of data in a SQL database.

Now, as with new URLs, the models must be linked to the Django project. In `djangoair/settings.py`, there is a list called `INSTALLED_APPS`, pre-populated with Django’s installed apps. To add the `flights` app, `flights.apps.FlightsConfig` should be added to that list.

`FlightsConfig` is the class the defines the settings for the `flights` application.

### Migrations
When building a web application, very rarely will all the tables be defined with all the correct columns from the beginning. Usually, tables are built up as the application grows, and the database will be modified. It would be tedious to change both the Django model code and run the SQL commands to modify the database.

Django’s solution to this problem is **migrations**. Django automatically detects and changes to `models.py` and automatically generates the necessary SQL code to make the necessary changes.

To create the table for managing flights inside the database, run `python manage.py makemigrations`. This will look through model files for any changes and generate a migration, which represents the necessary changes for the database. Running this command will create a file `migrations/0001_initial.py`:

```py
# Generated by Django 2.0 on 2018-07-19 22:14

from django.db import migrations, models

class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='Flight',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('origin', models.CharField(max_length=64)),
                ('destination', models.CharField(max_length=64)),
                ('duration', models.IntegerField()),
            ],
        ),
    ]
```

Inside the class `Migration` is a list `operations`, which contains everything that should happen to the database. In this case, the model `Flight` should be created, with fields `id`, `origin`, `destination`, and `duration`.

Note that `id` was never specified in `models.py`. Django adds this column by default.

The command `python manage.py sqlmigrate flights 0001` will produce the SQL code that actually corresponds to this migration. This command doesn’t need to be run, but it is helpful in demonstrating what’s actually going on. The SQL command is very similar to what has been shown before, but it didn’t need to be written. It is all generated by Django’s migration system.

To actually apply this migration to the database, run `python manage.py migrate`, which will apply the new migration as well as some default Django ones.

The database that is actually being used here is defined in `djangoair/settings.py` in the `DATABASES` dictionary. By default, it uses a SQLite 3 (another version of SQL that uses a local file for a database) and the database file `db.sqlite3`.

### Django Shell
Django provides a shell, similar to Python’s interpreter, that allows for direct modification of the database. Start the shell by running `python manage.py shell`. Inside the shell, Python commands can be run.

To create a new flight, the following commands can be run inside the shell.

```py
from flights.models import Flight

f = Flight(origin="New York", destination="London", duration=415)
f.save()

Flight.objects.all()
# Returns <QuerySet [<Flight: Flight object(1)>]>
```

`f.save()` is analogous to SQL’s `COMMIT`.

A `QuerySet` is like a list, with added functionality.

The representation of the `QuerySet` that the shell returns isn’t really readable or helpful. To produce a more useful, string representation of a flight, a `__str__` function can be added to `Flight` class in `flights/models.py`.

```py
def __str__(self):
    return f"{self.id} - {self.origin} to {self.destination}"
```

For any class, not just in Django, a `__str__` function defines what an object should like look when printed, whether to a terminal, an HTML page, etc.

Back to the shell:

```py
Flight.objects.all()
# Returns <QuerySet [<Flight: 1 - New York to London>]>

f = Flight.objects.first()

f
# Returns <Flight: 1 - New York to London>

f.origin()
# Returns 'New York'

f.id
# Returns 1

f.delete()
# Deletes the flight as expected
```

### Better Models
A more robust design for the `Flight` model would have an id field that links to a table of airports instead of just text for origins and destinations. To do so, a new `Airport` model must first be created.

```py
class Airport(models.Model):
    code = models.CharField(max_length=3)
    city = models.CharField(max_length=64)

    def __str__(self):
        return f"{self.city} ({self.code})"
```

Then, the `Flight` model can be modified appropriately, with origin and destination being `ForeignKey`s.

```py
class Flight(models.Model):
    origin = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="departures")
    destination = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="arrivals")
```

Django models allow for specific behavior when an airport, for instance, is deleted. `on_delete=models.CASCADE` means that if an airport is deleted, all flights with that airport as an origin or destination will be deleted as well.

`related_name` allows for the accessing of all flights departing from or arriving at a particular airport, using the keys `deparatures` or `arrivals`.

Note that there is no literal definitions of origin and destination as IDs, nor any actual commands for how to associate the two tables. The only things specified is that `origin` and `destination` should be `Airports`. All of the work to make that happen is left to Django.

To apply these changes, they must be migrated in the same fashion as before. Now, in the shell, it’s a lot easier and more intuitive to create flights.

```py
from flights.models import Airport, Flight

jfk = Airport(code="JFK", city="New York City")
lhr = Airport(code="LHR", city="London")
jfk.save()
lhr.save()

f = Flight(origin=jfk, destination=lhr, duration=415)
f.save()

f.origin
# Returns <Airport: New York City (JFK)>

f.origin.code
# Returns 'JFK'

jfk.departures.all()
# Returns <QuerySet [<Flight: 1 - New York City (JFK) to London (LHR)>]>
```