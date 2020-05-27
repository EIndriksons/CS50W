# Lecture 4 - Object Oriented Programming, API's

## Object Oriented Programming
Python, along with many other programming langauges, use Object-Oriented Programming (OOP). An **object** is a discrete item. OOP allows for the creation of classes, which are the generic forms of objects. For example, a *flight* class defines all the components which describe a flight, as well as actions that a flight should be able to take, such as adding a passenger. Similarly, a *passenger* class would represent the generic idea of passenger, defined by a name and associated with a flight, perhaps.

Here’s a simple example of a Python class.

```py
class Flight:

    def __init__(self, origin, destination, duration):
        self.origin = origin
        self.destination = destination
        self.duration = duration
```

`__init__` is a **method**, which is a function performed on individual objects. `__init__` in particular is a special, built-in method that describes what should happen when a flight object is created.

Generally, methods take `self` as their first argument. `self` refers to the object being worked with. The other three arguments are simply the information that should be stored about a particular flight. That information is stored as **properties** inside the object, using dot notation.

Here’s how the `Flight` class might be used:

```py
# Create flight.
f = Flight(origin="New York", destination="Paris", duration=540)

# Change the value of a propety.
f.duration += 10

# Print details about flight.
print(f.origin)
print(f.destination)
print(f.duration)
```

Note that only flight information is passed in to `Flight()`; the `self` argument to the `__init__` method is automatically specified.

`f` is a variable of type `Flight`, just like a variable might be of type `str` or `int`.

Additional methods can be added to the `Flight` class:

```py
class Flight:

    # assume same __init__ method

    def print_info(self):
        print(f"Flight origin: {self.origin}")
        print(f"Flight destination: {self.destination}")
        print(f"Flight duration: {self.duration}")

def main():
    f1 = Flight(origin="New York", destination="Paris", duration=540)
    f1.print_info()
```

Now, this functionality of printing out flight info can be used with any flight object that might be created. Each time, `self` refers to the object that the method is being called on. In this example, that’s `f1`.

Methods can also take additional arguments and modify properties.

```py
def delay(self, amount):
    self.duration += amount
```

Note that writing methods like `delay` and `print_info`, as well just the idea of `Flight` class in general, allow for **abstraction**. The `Flight` class and all of its methods can be used in a logical and easily understood way without needing to know or even understand how `Flight` may be implemented.

Given a simple `Passenger` class…

```py
class Passenger:

    def __init__(self, name):
        self.name = name
```

A more complex `Flight` class can be implemented.

```py
class Flight:

    counter = 1

    def __init__(self, origin, destination, duration):

        # Keep track of id number.
        self.id = Flight.counter
        Flight.counter += 1

        # Keep track of passengers.
        self.passengers = []

        # Details about flight.
        self.origin = origin
        self.destination = destination
        self.duration = duration

    def print_info(self):
        print(f"Flight origin: {self.origin}")
        print(f"Flight destination: {self.destination}")
        print(f"Flight duration: {self.duration}")

        print()
        print("Passengers:")
        for passenger in self.passengers:
            print(f"{passenger.name}")

    def add_passenger(self, p):
        self.passengers.append(p)
        p.flight_id = self.id
```

Note that `counter` is defined outside of the `__init__` function and is not specific to individual flights (it’s not defined as `self.counter`. This means that all flight objects can see this same counter variable, which allows for the implementation the `id` property shown here. Similar to the SQL database which had an auto-incrementing `id` column, the `id` property of flights will automatically incrememt as new flight objects are created.

The `passengers` property of `Flights` is going to be a list of `Passenger` objects.

In `add_passenger`, `p.flight_id` is created, because `flight_id` is not defined in the Passenger class’s `__init__`.

Here’s how the more advanced `Flight` class could be used:

```py
# Create flight.
f1 = Flight(origin="New York", destination="Paris", duration=540)

# Create passengers.
alice = Passenger(name="Alice")
bob = Passenger(name="Bob")

# Add passengers.
f1.add_passenger(alice)
f1.add_passenger(bob)

f1.print_info()
```

## Object Relational Mapping
Object-Relational Mapping, or ORM, allows for the combination of the OOP world of Python and the relational database world of SQL. With ORM, Python classes, methods, and objects become the tools for interacting with SQL databases. To do this, the Flask-SQLAlchemy package will be used.

The basic setup, inside `models.py`:

```py
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Flight(db.Model):
    __tablename__ = "flights"
    id = db.Column(db.Integer, primary_key=True)
    origin = db.Column(db.String, nullable=False)
    destination = db.Column(db.String, nullable=False)
    duration = db.Column(db.Integer, nullable=False)


class Passenger(db.Model):
    __tablename__ = "passengers"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String, nullable=False)
    flight_id = db.Column(db.Integer, db.ForeignKey("flights.id"), nullable=False)
```

For any table inside of the database, there is one class defined inside `models.py`.

Adding `db.Model` in parentheses after class names indicates that these classes **inherit** from `db.Model`. The details of inheritance are unimportant right now. Simply, this allows for the class to have some built-in relationship with SQLAlchemy to interact with the database.

`__tablename__` naturally corresponds with the table name inside the database.

Every property is defined as a `db.Column`, which will become columns in the table. The arguments to `db.Column` are naturally similar to those used for table creation in SQL.

Note that `flights.id` is marked as a foreign key using the `__tablename__` `flights`, not the class name `Flight`.


Now that there’s a defined structure for how the tables should look, they can be created inside a Flask application.

```py
import os

from flask import Flask, render_template, request

# Import table definitions.
from models import *

app = Flask(__name__)

# Tell Flask what SQLAlchemy databas to use.
app.config["SQLALCHEMY_DATABASE_URI"] = os.getenv("DATABASE_URL")
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

# Link the Flask app with the database (no Flask app is actually being run yet).
db.init_app(app)

def main():
    # Create tables based on each table definition in `models`
    db.create_all()

if __name__ == "__main__":
    # Allows for command line interaction with Flask application
    with app.app_context():
        main()
```

## Python Versions of SQL Queries

### `CREATE`
`db.create_all()` is the Python-Flask-SQLAlchemy’s version of the `CREATE` SQL command.

### `INSERT`
SQL’s `INSERT`…

```sql
INSERT INTO flights
    (origin, destination, duration)
    VALUES ('New York', 'Paris', 540)
```

…and Python’s `INSERT`.

```py
  flight = Flight(origin="New York", destination="Paris", duration=540)
  db.session.add(flight)
```

SQlAlchemy automatically takes care of SQL transactions with `db.session`.

### `SELECT`
SQL’s `SELECT`…

```sql
SELECT * FROM flights;
SELECT * FROM flights
    WHERE origin = 'Paris';
SELECT * FROM flights
    WHERE origin = 'Paris' LIMIT 1;
SELECT COUNT(*) FROM flights
    WHERE origin = 'Paris';
SELECT * FROM flights WHERE id = 28;
SELECT * FROM flights
    ORDER BY origin;
SELECT * FROM flights
    ORDER by origin DESC;
SELECT * FROM flights
    WHERE origin != 'Paris';
SELECT * FROM flights
    WHERE origin LIKE '%a%';
SELECT * FROM flights
    WHERE origin IN ('Tokyo', 'Paris');
SELECT * FROm flights
    WHERE origin = "Paris"
    AND duration > 500;
SELECT * FROm flights
    WHERE origin = "Paris"
    AND duration > 500;
SELECT * FROM flights JOIN passengers
    ON flights.id = passengers.flight_id;
```

…and Python’s `SELECT`:

```py
Flight.query.all()
Flight.query.fliter_by(origin="Paris").all()
Flight.query.filter_by(origin="Paris").first()
Flight.query.filter_by(origin="Paris").count()
Flight.query.get(28)
Flight.query.order_by(Flight.origin).all()
Flight.query.order_by(Flights.origin.desc()).all()
Flight.query.filter(Flight.origin != "Paris").all()
Flight.query.filter(Flight.origin.like("%a%")).all()
Flight.query.filter(Flight.origin.in_(["Tokyo", "Paris"])).all()
Flight.query.filter(and_(Flight.origin == "Paris", Flight.duration > 500)).all()
Flight.query.filter(or_(Flight.origin == "Paris", Flight.duration > 500)).all()
db.session.query(Flight, Passenger).filter(Flight.id == Passenger.flight_id).all()
```

### `UPDATE`
SQL’s `UPDATE`…

```sql
UPDATE flights SET duration = 280
    WHERE id = 6;
```

…and Python’s `UPDATE`:

```py
flight = Flight.query.get(6)
flight.duration = 280
```

### `DELETE`
SQL’s `DELETE`…

```sql
DELETE FROM flights WHERE id = 28;
```

…and Python’s `DELETE`:

```py
flight = Flight.query.get(28)
db.ksession.delete(flight)
```

### Other
Some other miscellaneous SQL commands…

```sql
COMMIT;
```

…and their Python parallels.

```py
db.session.commit()
```

Before, when importing data from a CSV file, SQL code had to be written directly into the Python file. Now, SQLAlchemy can take care of that behind the scenes.

```py
import csv

# Same setup code as before.

def main():
    f = open("flights.csv")
    reader = csv.reader(f)
    for origin, destination, duration in reader:
        flight = Flight(origin=origin, destination=destination, duration=duration)
        db.session.add(flight)
        print(f"Added flight from {origin} to {destination} lasting {duration} minutes.")
    db.session.commit()
```