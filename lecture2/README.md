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