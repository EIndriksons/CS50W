# Lecture 2 - Flask

## Python
Python 3.6 will be used which is an interpreted language that will be used in the context of this class to generate dynamic websites and web applications.

Some basic Python syntax:
- `print("Hello, world!")` - Print a string to the screen
- `print(f"Hello, {name}!")` - Print a format string (variable names enclosed with `{}` will be replaced by variable values)
- `name = input()` - Set variable `name` to the user input returned by `input()`
- Conditional statement:
    *   ```python
        if x > 0:
            print("x is positive")
        elif x < 0:
            print("x is negative")
        else:
            print("x is zero")
        ```
    * `elif` and `else` blocks are optional.
    * **Note** that indentation in Python is not stylistic, but rather is used to demarcate blocks of code. In this example, the Python interpreter knows where the conditional `if` block ends and the `elif` block begins because of the changes in indentation.

### Data Types
- `int` : integer value
- `float` : floating point value
- `str` : text string
- `bool` : boolean value (True or False)
- `None` : empty value
- **Note** that Python is a weakly typed language.

### Sequences
**Strings** are justs sequence of characters, and can be indexed as such.

```python
name = "Alice"
print(name[0])
```

**Tuples** are immutable collections of values under a single name, which can be indexed positionally.

```python
coordinates = (10.0, 20.0)
print(coordinates[1])
```

**Lists** are mutable collections of values under a single name, which can be indexed positionally. Indexing out of range raises a Python ‘exception’. In this case, an `IndexError`, because there is no fourth value in `names` for Python to return.

```python
names = ["Alice", "Bob", "Charlie"]
print(names[2])
```

**Sets** are unordered collection of unique items. Because they are unordered, they cannot be indexed. `s` is a `set`, an unordered collection of unique items.

```python
s = set()
s.add(1)
s.add(3)
s.add(5)
```

**Dictionaries** (or dicts) are like lists, except that they are unordered and their values are indexed by `keys`. The `+=` operator increments the left-hand side by the right-hand side.

```python
ages = {"Alice": 22, "Bob": 27}
print(ages["Alice"])
ages["Alice"] += 1
```

**Note** that any sequence in Python can contain any number of data types.

### Loops
For-loops iterate over their bodies a limited number of times. In this case, the number of iterations is set by `range(5)`.

`range(5)` returns the sequence of numbers starting at 0 through 4. Each value is passed to `i` once, resulting in the loop running a total of 5 times. `i` is normally referred to as an iterator variable.

```py
for i in range(5):
    print(i)
```

This for-loop iterates over `names`, which is a list. Every value in the list is assigned, in order, to the iterator `name` once.

```py
for name in names:
    print(name)
```

### Functions
Python has built-in functions, such as `print()` and `input()`, but Python also allows for the creation of user-defined functions.

```py
def square(x):
    return x * x
```

This is a function called `square`, which takes a single argument `x`, and returns the value `x * x`.

Like loops, the body of a function must be indented.

This loop, which prints out the results of `square` with a range of arguments, using an older method for format strings.

```py
for i in range(10):
    print("{} squared is {}".format(i, square(i)))
```

Trying to call a function that hasn’t been defined will raise a `NameError` exception.