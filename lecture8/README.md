# Lecture 8 - Testing, CI/CD


## Testing
As web applications become increasingly sophisticated and complex, it becomes increasingly important to thoroughly test them. Testing ensures that changes to a function used in many places don’t cause completely different parts of the application to break. Or, functions may behave differently for different types of input, so all types of input should be tested to ensure that the application handles them well.

Here’s a basic function, completely separate from any web application, to showcase the idea of testing.

```py
import math

def is_prime(n):
    """Determines if a non-negative integer is prime."""
    if n < 2:
        return False
    for i in range(2, int(math.sqrt(n)):
        if n % i == 0:
            return False
    return True
```

This function checks if an integer is prime by ensuring that it’s above 2 (the smallest prime) and that it is not evenly divisble by any number less than its square root.

One way to test this function would be to simply run this function in the Python interpreter and manually test it. While this might work for a small example, this will ultimately become tedious. The next best step is to write a test function.

```py
from prime import is_prime

def test_prime(n, expected):
    if is_prime(n) != expected:
        print(f"ERROR on is_prime({n}), expected {expected}")
```

A simple way to use this test function is to write a short Python program that runs this test for a series of inputs. That might look like this:

```py
from prime import is_prime

def test_prime(n, expected):
    if is_prime(n) != expected:
        print(f"ERROR on is_prime({n}), expected {expected}")

if __name__ == "__main__":
    test_prime(-4, False)
    test_prime(-3, False)
    test_prime(-2, False)
    test_prime(-1, False)
    test_prime(0, False)
    test_prime(1, False)
    test_prime(2, True)
    test_prime(3, True)
    test_prime(4, False)
```

This can be run in the terminal with `python tests0.py`.

Now that the testing is automated, when a change is made to `prime.py`, it is easy to see if the problem is solved.

```py
for i in range(2, int(math.sqrt(n) + 1):
    if n % i == 0:
        return False
```

This was a simple off-by-1 error. `range` returns values starting at the first argument and up to, but not including, the second.

Running the test file again after this change will produce no errors. This does not mean the function is totally correct, only that the given tests have been passed. Now, the challenge is to write good, comprehensive tests that cover all possible conditions. In this case, that might mean testing even and odd numbers, etc. This becomes increasingly important for larger applications.

Remember also that we do not necessarily only run tests when we think things might break. Sometimes, we might do so after refactoring our code, trying to optimize its functionality. The above `is_prime()` function could, perhaps, be optimized in a more “Pythonic” way (Python is somewhat notorious for examples like this) as follows:

```py
import math

def is_prime(n):
    return n > 1 and all(n % i for i in range(2, int(math.sqrt(n)) + 1))
```

Breaking this down, there are two conditions being checked:
- Is `n` greater than 1; and
- Is it the case that all of the values from 2 up to (and including) the square root have a “truthy” value of `True`? (If so, that means `n % i` is nonzero for all of them; i.e., n is not evenly divisible by any of them.)

If, and only if, both of those are true, is the number considered prime!


### `assert`
A useful Python feature for testing is the built-in `assert` command, which is followed by an boolean expression. If it does not evaluate to `True`, Python will throw an `AssertionError`.

All programs, on exit, return an exit code. Generally speaking, an exit code of 0 indicates that everything went well, and any other code indicates an error. To examine an exit code in bash after running a Python script, use `echo $?`.


### `unittest`
`unittest` is a built-in Python library for testing. Testing `is_prime` with `unittest` might look like this:

```py
import unittest

from prime import is_prime


class Tests(unittest.TestCase):

    def test_1(self):
        """Check that 1 is not prime."""
        self.assertFalse(is_prime(1))

    def test_2(self):
        """Check that 2 is prime."""
        self.assertTrue(is_prime(2))

    def test_8(self):
        """Check that 8 is not prime."""
        self.assertFalse(is_prime(8))

    def test_11(self):
        """Check that 11 is prime."""
        self.assertTrue(is_prime(11))

    def test_25(self):
        """Check that 25 is not prime."""
        self.assertFalse(is_prime(25))

    def test_28(self):
        """Check that 28 is not prime."""
        self.assertFalse(is_prime(28))


if __name__ == "__main__":
    unittest.main()
```

`Tests` inherits from `unittest.TestCase`, which signifies that it contains a series of tests, each of which is capable of extending the basic functionality defined in `unittest.TestCase`.

Each `test` inside `Tests` is simply a method with an appropriate “docstring” labelling it.

`unittest` has a series of built-in, more advanced and readable assert statements. Instead of using `assert isPrime(1) == False`, simply use `self.assertFalse(is_prime(1))`.

`unittest.main()` will run all the tests.

`unittest` methods include (but are not limited to):
- `assertEqual`
- `assertNotEqual`
- `assertTrue`
- `assertFalse`
- `assertIn` : checks if an item is in a list
- `assertNotIn` : checks if an item is not in a list