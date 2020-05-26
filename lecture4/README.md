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