---
title: Intern study summary - Jul 12 Wed
date: 2023-07-12 21:49:46
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Learning Log,
    Intern At 300K,
    Python,
    Python Grammar,
    UnitTest,
    Numpy,
    Environment Variable,
    Click Library,
    Programming Tips,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Python

## Compare complex object

When I was doing unittest on my project, I got `ValueError: The truth value of an array with more than one element is ambiguous. Use a.any() or a.all()` for my `self.assertEqual()`

This error is due to trying to directly compare two arrays using the `assertEqual` function in the `unittest` module. The error message suggests that it's ambiguous to evaluate the truth value of an array with more than one element.

To fix this, you can use `numpy.testing.assert_array_equal` to compare numpy arrays element by element:

```python
import numpy as np

class TestModel(unittest.TestCase):
    ...
    def test_load_model_with_target_size(self):
        ...
        np.testing.assert_array_equal(self.sample_target_size, target_size)
    ...

```

This `assert_array_equal` function raises an AssertionError if two array-like objects are not equal. If they are not the same shape, they are considered unequal. If an element in one of the input arrays has a value of `NaN`, then the corresponding element in the other input is considered equal if it also has a value of `NaN`.

Note: `numpy` should be already installed if you're dealing with arrays. If not, you may need to install it using `pip install numpy`.

## One time `setUp()`

While unittesting, we usually need to set up some data before we can actually testing the function. Those setting up process could be duplicated code, which we should void. Thus, we could use the `setUp` function, which will be auto called before every test function ran.

Yet sometimes we use very complex `setUp` function to do some process that might take a lot of time. In this case, it would be better to only do the `setUp` once and make the rest of unittests use the result from that `setUp`.

Here is a conditional way of calling `setUp`, if something already been initialized, simply return and do not do `setUp` any more. Here, I will use the `id` as something we should only initialize once.

You can check if `self.id` exists in the instance dictionary of the class using `hasattr()`. The `hasattr()` function in Python is used to check if an object has the given named attribute and return true if present, else false.

Here is an example of how you could modify your `setUp()` method:

```python
import unittest

class TestMyClass(unittest.TestCase):
    def setUp(self):
        if not hasattr(self, 'id'):
            self.id = "some id" # Or use a function to generate the id

    def test_something(self):
        # Your test here
        pass

```

In this example, `setUp()` first checks if `self.id` exists. If not, it sets an ID. Now, you can use `self.id` throughout your tests without getting an `AttributeError`. This ID will be generated before each test runs, but only if it hasn't been generated before.

### Yet this way is not perfect, because:

The `setUp()` method in `unittest.TestCase` is designed to run before each test method, meaning the attributes set in it won't persist between tests, causing the `AttributeError` you're experiencing.

One possible solution is using the `setUpClass(cls)` method instead. This method is a class method, and it's run only once before any tests or the `setUp()` method is run in the test case. The attribute defined in this method should persist across different tests.

Here's how you can use it:

```python
import unittest

class TestModel(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        if not hasattr(cls, 'test_id'):
            cls.test_id = "some id"  # Set a test_id here

    def test_load_model_with_target_size(self):
        model, target_size = load_model_with_target_size(self.__class__.test_id)
        # rest of the test

    def test_read_model(self):
        model, config = load_model_from_db(self.__class__.test_id)
        # rest of the test

```

In this case, `test_id` is set as a class attribute, so it's shared among all instances of `TestModel`, allowing it to be accessed in any test method. Note the use of `self.__class__.test_id` in the test methods instead of `self.test_id`.

Also, ensure that your function `load_model_with_target_size()` and `load_model_from_db()` are implemented correctly and can handle the value of `test_id` that you are passing.

### But I cannot set some attribute to self if I used this method

You're correct that `setUpClass(cls)` is a class method and hence doesn't have access to instance-specific variables. The recommended way to have instance-specific setup is using the `setUp(self)` method, which gets run before every test method in the class. However, it will not retain the value of the variable between different tests.

If you want to set a value only once and use that value in multiple tests (while still having access to instance variables), you can use a combination of `setUpClass(cls)` and `setUp(self)`. Use `setUpClass(cls)` to set a value that's shared among all instances, and `setUp(self)` to assign that shared value to an instance variable.

Here's an example:

```python
import unittest

class TestModel(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        if not hasattr(cls, 'test_id'):
            cls.test_id = "some id"  # Set a test_id here

    def setUp(self):
        if not hasattr(self, 'id'):
            self.id = self.__class__.test_id

    def test_load_model_with_target_size(self):
        model, target_size = load_model_with_target_size(self.id)
        # rest of the test

    def test_read_model(self):
        model, config = load_model_from_db(self.id)
        # rest of the test

```

In this case, `setUpClass(cls)` sets `test_id` as a class attribute, which is shared among all instances. `setUp(self)` then assigns this class attribute to an instance attribute `self.id` before each test. Now, you can use `self.id` in your test methods. This way, you'll get the desired effect: `test_id` is set only once, and its value is used in each test via the instance attribute `self.id`.

Remember, this will give you the same `test_id` for all test methods in the class. If you want to use different IDs for different test methods, you'll have to generate a new ID within each test method itself.

## Environment variable operation

To set environment variables in Python, you can use the `os` module's `environ` dictionary. Here's an example of setting an environment variable:

```python
import os

# Set environment variable
os.environ['MY_ENV_VAR'] = 'my value'

# Get environment variable
print(os.getenv('MY_ENV_VAR'))  # Outputs: 'my value'

```

However, it's important to note that these environment variables are only set for the current process in which your Python script is running. If you exit this script or start a new one, the environment variables you set will not be available.

If you want to set environment variables that are available to all processes, you will need to do this outside of Python, typically in the command line for your operating system. The method varies depending on whether you are using Windows, macOS, or a Linux-based system. For instance, in Linux you could use the `export` command:

```python
export MY_ENV_VAR="my value"

```

And then access it in Python:

```python
import os

# Get environment variable
print(os.getenv('MY_ENV_VAR'))  # Outputs: 'my value'

```

Keep in mind that this will only set the environment variable for the duration of the current terminal session. If you want to set a persistent environment variable, you will need to add the `export` line to your shell profile file (like `.bashrc` or `.bash_profile` for bash, `.zshrc` for zsh, etc.).

## flag argument in click

Click is a Python library used for creating beautiful command line interfaces. To have an argument that acts as a flag to turn on some functionality, you would use an `option` rather than an `argument`.

Options in Click are optional (as the name suggests) and can be boolean flags. This is exactly what you want. Here is an example how you can implement it:

```python
import click

@click.command()
@click.option('--read-file', '-f', is_flag=True, help='Turn on file reading')
def my_program(read_file):
    """My Program that reads a file if -f is provided."""
    if read_file:
        # put your file reading code here
        click.echo('File reading turned on.')
    else:
        click.echo('File reading turned off.')

if __name__ == '__main__':
    my_program()

```

In the example above, `read_file` is a boolean that is `True` if `-f` or `--read-file` is passed on the command line, and `False` otherwise. The `is_flag=True` part is what makes this an on/off switch. Note the use of `option` instead of `argument` to denote that this is optional.

You can run the program with the `-f` option like this:

```python
$ python my_program.py -f
File reading turned on.

```

And without `-f`, it would be:

```python
$ python my_program.py
File reading turned off.

```

# Numpy

I met a problem when I was trying to read the data(the type is ndarray) I saved to my database: I found even the dimension of my data is different. I thought about it and found that the problem was about the `dtype`. When storing an ndarry, the default `dtype` is `np.float64`, yet while reading I used `np.float32`. Thus, I was thinking make the `dtype` as an input parameter:

# ndarray reading from file different from storing

the float

Please note that the string should match a valid numpy dtype. The full list of valid numpy dtypes can be found in the [NumPy documentation](https://numpy.org/devdocs/user/basics.types.html).

```python
result = from_buffer(target_size_byte, dtype='float64')

```

You can also use equivalent string values to represent the numpy dtype. For example, you could replace `np.float64` with the string `'float64'`.

In this function `from_buffer`, the `dtype` is a parameter where you can specify the desired data type.

```python
import numpy as np

def from_buffer(target_size_byte, dtype):
    target_size = np.frombuffer(target_size_byte, dtype=dtype)
    return target_size

# Usage
result = from_buffer(target_size_byte, dtype=np.float64)

```

If you want to set the `np.float64` as an input parameter to your function, you can simply use a variable that you pass to your function. This variable should be of type `numpy.dtype` or any equivalent type string. Here is an example:
