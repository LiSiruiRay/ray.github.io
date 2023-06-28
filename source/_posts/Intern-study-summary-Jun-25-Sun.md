---
title: Intern study summary - Jun 25 Sun
date: 2023-06-25 21:37:02
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Learning Log,
    Intern At 300K,
    Python,
    Python Grammar,
    Tech,
    I/O,
    Error Explanation,
    RESTful API,
    Command Line,
    PyCharm Professional,
    URL,
    Programming Tips,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

This blog is create on the day of 2023-06-28 21:37:02. Yet, to maintain the order, I changed the creating date.

# Python

## Package & Directory (folder)

In Python, a directory becomes a package when it contains a special file named `__init__.py`. This file can be empty, and it indicates that the directory should be treated as a Python package. A Python package is simply a way of organizing related modules (`.py` files) into a directory hierarchy.

Essentially, a package in Python is also a directory, but not all directories are packages. If you have a directory with Python files but no `__init__.py`, Python will treat it as a directory of scripts, not as a package.

Let's say you have a directory called `utils` and inside it, you have some Python scripts like `script1.py`, `script2.py`, etc. If you want to import functions from these scripts in other parts of your code like so:

```python
from utils.script1 import my_function
```

You would need to make `utils` a package by adding an `__init__.py` file inside it. If you don't add an `__init__.py` file, Python won't recognize `utils` as a package and you won't be able to import from it in this manner.

To summarize, if you just want a bunch of Python scripts in a folder and you're not concerned about importing from them, you can just use a regular directory. But if you want to be able to import functions/classes from these scripts as part of a package, you should add an `__init__.py` file to the directory, turning it into a package.

## `I/O` in python

### Create folders by python

You can create folders (directories) in Python using the `os` module's `mkdir` or `makedirs` function.

Here's how to create a single folder:

```python
import os

os.mkdir("my_folder")
```

This will create a folder named "my_folder" in the current directory.

If you want to create a nested directory structure, you can use `os.makedirs`:

```python
import os

os.makedirs("my_folder/sub_folder/sub_sub_folder")
```

This will create the entire path, even if the intermediate folders ("my_folder" and "my_folder/sub_folder") do not exist.

Note that these functions will raise a `FileExistsError` if the folder you're trying to create already exists. If you want to avoid this, you can check if the directory exists before creating it:

```python
import os

if not os.path.exists("my_folder"):
    os.mkdir("my_folder")
```

Or, if you're using Python 3.7 or later, you can use the `exist_ok` parameter with `os.makedirs`:

```python
import os

os.makedirs("my_folder/sub_folder/sub_sub_folder", exist_ok=True)
```

This will not raise an error if the folder already exists.

### Get a list of folder's name

You can get a list of folder names in a directory using the `os` module's `listdir` or `os.scandir` functions.

Here's a simple example using `os.listdir`:

```python
import os

folder_path = "/path/to/your/folder"
folders = [name for name in os.listdir(folder_path) if os.path.isdir(os.path.join(folder_path, name))]

print(folders)
```

This script will give you a list of all the directories (not files) in the specified folder. The `os.path.isdir` function checks if the path is a directory.

Here's an example using `os.scandir`, which is a bit more efficient if you're doing additional operations like checking if the paths are directories:

```python
import os

folder_path = "/path/to/your/folder"
folders = [entry.name for entry in os.scandir(folder_path) if entry.is_dir()]

print(folders)
```

Both these scripts will print the names of the folders in the specified directory. Note that these are just the folder names, not their full paths. If you want the full paths, replace `entry.name` with `entry.path` in the second example, or `name` with `os.path.join(folder_path, name)` in the first.

### Create a file

You can create a file in Python using the built-in `open` function with the `w` (write) mode. Here's an example:

```python
with open('myfile.txt', 'w') as f:
    pass
```

This code will create a file named `myfile.txt` in the current directory. If the file already exists, this will overwrite it.

The `with` statement is used for context management, it ensures that the file is properly closed after it is no longer needed.

If you want to write some data to the file when you create it, you can do that like this:

```python
with open('myfile.txt', 'w') as f:
    f.write("Hello, World!")
```

This code will create `myfile.txt` and write the string `Hello, World!` to it.

If you want to create the file without overwriting it if it already exists, you can use the `x` (exclusive creation) mode:

```python
try:
    with open('myfile.txt', 'x') as f:
        pass
except FileExistsError:
    print("The file already exists.")
```

This code will only create the file if it doesn't already exist. If the file does exist, a `FileExistsError` is raised, and the code in the `except` block is executed.

### how to delete a file

You can delete a file in Python using the `os` module's `remove` function. Here's a simple example:

```python
import os

file_path = "/path/to/your/file.txt"
os.remove(file_path)
```

This code will delete the file at the specified path.

Please be careful, because this operation cannot be undone. Once a file is deleted in this way, it cannot be recovered.

If you want to make sure that a file exists before you try to delete it, you can use `os.path.isfile`:

```python
import os

file_path = "/path/to/your/file.txt"

if os.path.isfile(file_path):
    os.remove(file_path)
else:
    print(f'Error: {file_path} not a valid filename')
```

This code will only try to delete the file if it exists. If the file doesn't exist, it will print an error message instead.

## **`unexpected EOF while parsing`**

The error message `unexpected EOF while parsing` typically means that the Python interpreter has reached the end of your file (EOF stands for End Of File) while it was expecting to find something else. This is typically due to unmatched or improperly used parentheses, brackets, or quotes.

For example, this error could happen if you have an opening parenthesis or bracket without a closing one:

```python
# Missing closing parenthesis
print("Hello, world!"

# Missing closing bracket
my_list = [1, 2, 3
```

Or if you have opened a string with a quote but never closed it:

```python
# Missing closing quote
print("Hello, world)
```

Without the rest of your code, I can't tell you exactly what's causing the problem. But you should go through your code and make sure that all parentheses, brackets, and quotes are properly matched and closed. Using an IDE or a text editor with syntax highlighting can be very helpful for this, as it will often visually indicate unmatched parentheses or quotes.

## `Parameter 'filename' unfilled`

When a Python function, method, or constructor (the `__init__` method in a class) requires arguments, you must provide those arguments whenever you call the function, method, or constructor. If you don't, you will see an error message or a warning depending on your development environment.

The message "Parameter 'filename' unfilled" is telling you that you are calling a function or method that requires a parameter named 'filename', but you are not providing a value for that parameter.

For example, suppose you have the following function:

```python
def print_file_contents(filename):
    with open(filename, 'r') as file:
        print(file.read())
```

If you call `print_file_contents()` without providing an argument, like this:

```python
print_file_contents()
```

Then you will see an error because you didn't fill in the 'filename' parameter. The correct way to call the function is to provide the filename as an argument, like this:

```python
print_file_contents('myfile.txt')
```

This error can also occur if you're using an Integrated Development Environment (IDE) or editor that provides code checking or linting services, and you've written a function call without filling in all the required parameters. In this case, the IDE will flag the incomplete function call as an error even before you run the code.

## Key check in Python

`has_key()` in python:

The `has_key()` method in Python dictionaries was deprecated and removed in Python 3. It was used to check if a given key exists in a dictionary. Instead of using `has_key()`, you can directly use the `in` operator to check for the presence of a key in a dictionary.

Here's an example of how you can check if a key exists in a dictionary using the `in` operator:

```python
my_dict = {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}

if 'key2' in my_dict:
    print("Key 'key2' exists in the dictionary.")
else:
    print("Key 'key2' does not exist in the dictionary.")
```

Output:

```bash
Key 'key2' exists in the dictionary.
```

Using the `in` operator is the recommended way to check for the presence of a key in a dictionary in Python.

## Hex in python

### Example:

Convert str of hex to a hex in python, and add one

To convert a string representation of a hexadecimal number to an integer, use the `int` function in Python with base 16. Then you can add one to this value as it is now an integer. Here's an example:

```python
hex_str = "0x1a"
value = int(hex_str, 16)
value += 1
print(value)
```

This will output `27` (1a in hexadecimal is 26 in decimal).

If you want the result to remain in hexadecimal format, you can then convert it back to a hexadecimal string using the `hex` function:

```python
hex_str = "0x1a"
value = int(hex_str, 16)
value += 1
new_hex_str = hex(value)
print(new_hex_str)
```

This will output `0x1b` (27 in decimal is 1b in hexadecimal).

### Hex to Decimal

The conversion of a hexadecimal (hex) number to a decimal number in Python can be done using the built-in `int()` function, specifying the base as 16 (since hexadecimal is base 16).

Here's an example:

```python
hex_number = "0x1a" # hex_number is a string representation of a hexadecimal number.
dec_number = int(hex_number, 16) # dec_number is now a decimal (base 10) representation of hex_number.
print(dec_number)

```

This will output: `26`

In this case, the hexadecimal number `1a` corresponds to the decimal number `26`.

The `int()` function in Python can convert a string representation of a number in any base (between 2 and 36) to a decimal integer. When you pass in '16' as the second argument, you're telling it to interpret the string as a hexadecimal number.

# RESTful API

RESTful API is a type of web API that uses HTTP methods to interact with resources. REST stands for Representational State Transfer, and it's a set of design principles for creating networked applications.

Here's a brief overview of the basic principles of REST:

1. **Client-Server Architecture:** REST APIs follow a client-server model, where the client sends a request to the server and waits for a response. The client and server are independent and can be developed separately.
2. **Stateless:** Each request from the client to the server must contain all the information needed to understand and process the request. The server shouldn't store any information about the client's state between requests.
3. **Cacheable:** Responses from the server can be cached by the client. This can improve performance by reducing the need for repeated requests.
4. **Uniform Interface:** REST APIs should have a consistent, uniform interface, which makes them easier to use. This involves using standard HTTP methods (like GET, POST, PUT, DELETE), using resource URIs to identify resources, and using standard HTTP response codes.
5. **Layered System:** REST APIs can be composed of multiple layers, each performing specific tasks. For example, there might be a layer for handling requests, a layer for processing data, and a layer for storing data.

A RESTful API is typically used to allow different software systems to communicate with each other. In your case, you would use a RESTful API to allow your Swift front-end to communicate with your Python back-end. The Swift code would send HTTP requests to the API, and the API would respond with the requested data.

# `cat`

You can use `cat` command in the terminal to check the content of a file.

# PyCharm Python interpreter problem

Look at the following situation and see if you are able to figure it out:

I am working on a flask project. have finished the project before with PyCharm and I am using the PyCharm to open it. Now it is prompting that package requirements 'python-dotev == 1.0.0', 'Flask == 2.3.2' not satisfied. I clicked on the install package, got "Cannot set up a python SDK at Python 3.8 (serverProj) (/Users/ray/rayfile/serverProj/venv/bin/python). The SDK seems invalid.”

Solution:

This issue seems to be due to a problem with the Python interpreter selected for your project in PyCharm. The interpreter might be improperly configured, or the virtual environment associated with the project might be corrupted.

Here are some steps you can follow to try to resolve this:

1. **Ensure that Python is correctly installed**: Open a terminal (outside of PyCharm) and type `python3 --version`. This should return the version of Python installed. If it doesn't, you might need to install Python.
2. **Recreate the virtual environment**: It's possible that your project's virtual environment is corrupted. Navigate to your project's directory in terminal, delete the existing 'venv' directory (`rm -rf venv`), and create a new one using `python3 -m venv venv`.
3. **Reconfigure your PyCharm interpreter**: In PyCharm, go to Preferences -> Project -> Python Interpreter. Add a new interpreter, and select "Existing environment". Navigate to the Python executable in your newly created 'venv' directory (it should be at /path-to-your-project/venv/bin/python). Set this as your project interpreter.
4. **Reinstall the packages**: Try installing 'python-dotenv' and 'Flask' again. You can do this in PyCharm or from the terminal by activating your virtual environment (`source venv/bin/activate`), and then using pip to install the packages (`pip install python-dotenv==1.0.0 Flask==2.3.2`).

# `?` and `&` in URL

Here I will provide an example of RESTful API to better illustrate what a RESTful API is and dive deeper in to the URL’s world.

---

## Example:

While I was developing an API for data querying for the company, I had the following requirement:

I need to write a function in python flask with the entry point as "/data-api/top-pairs/<exchange>?endDate=<end_date>&days=<days>".

Query string `endDate` and `days` are optional

Default `endDate` = current UTC date and default `days` is 7.

The function returns the link

**Solution**

Here's how you could define a Flask route with the endpoint `/data-api/top-pairs/<exchange>`, accepting optional query parameters `endDate` and `days`.

The function first checks if the query parameters are present in the request, and if not, it assigns them default values. It then constructs and returns a link.

```python
from flask import Flask, request
from datetime import datetime, timedelta
import pytz

app = Flask(__name__)

@app.route('/data-api/top-pairs/<exchange>', methods=['GET'])
def get_top_pairs(exchange):
    # Get query parameters, provide default values if not present
    end_date = request.args.get('endDate', datetime.now(pytz.utc).strftime('%Y-%m-%d'))
    days = request.args.get('days', 7)

    # Construct and return the link
    link = f"/data-api/top-pairs/{exchange}?endDate={end_date}&days={days}"
    return link

if __name__ == '__main__':
    app.run(debug=True)
```

In this example, `exchange` is part of the URL, while `endDate` and `days` are optional query parameters. The function `get_top_pairs()` extracts these parameters using `request.args.get()`, providing default values of the current date (in UTC) and 7 days if they're not present. Then, it constructs a link string and returns it.

Note: This code uses Flask's built-in request object to access the query parameters. The `args` attribute is a MultiDict object that contains all the received query parameters. The `get()` method is used to access a parameter value, and allows you to provide a default value if the parameter is not found.

## Dive deeper

What is the `?` and `&` in the link? I always see those stuff in a weblink. Why here the string in the `@app.route()` is without `endDate` and `days`?

In a URL, the `?` and `&` symbols are used to specify query parameters.

- The `?` symbol is used to start the query parameters section of a URL. It separates the path portion of the URL from the query parameters.
- The `&` symbol is used to separate multiple query parameters.

So, in the URL `https://example.com/page?param1=value1&param2=value2`, `param1` and `param2` are query parameters with respective values of `value1` and `value2`.

Regarding your second question, in the `@app.route()` decorator, you only specify the path portion of the URL. This is because the route is used to map a URL to a specific function in your Flask application.

In your case, the route is `/data-api/top-pairs/<exchange>`. The `<exchange>` part is a variable part of the URL and Flask captures the value in this part of the URL and passes it to your function as an argument.

The query parameters (`endDate` and `days` in your case) are not part of the route because they are optional and their order does not matter. They are accessed inside your function using `request.args.get()`. This is a dictionary-like object that allows you to access the query parameters by their name.

The route does not need to know about these parameters. It only needs to know about the fixed and variable parts of the path of the URL. It's the job of your function to handle the query parameters, not the route.

# Tips for preventing typos

I hate typo!!!!!!!!!!!!!!!!!!!!!

Typos can sometimes be very tricky to spot, especially when they result in valid but incorrect code, as was the case here. They can be a common source of bugs that are hard to find.

Here are a few suggestions to minimize the impact of typos:

1. **Use an Integrated Development Environment (IDE)**: Most IDEs have features like syntax highlighting and autocomplete that can help spot typos. Some can even detect some types of bugs before you even run your code.
2. **Code Reviews**: Having another set of eyes look over your code can often spot mistakes that you might have missed.
3. **Testing**: Writing tests for your code can help ensure that it's doing what you expect and can help catch bugs introduced by typos.
4. **Take Breaks**: Sometimes, if you've been looking at the same piece of code for a long time, it can be hard to spot mistakes. Taking a short break can help reset your perspective and make it easier to see errors.
5. **Pair Programming**: This is a technique where two programmers work together at one workstation. One, the driver, writes the code while the other, the observer or navigator, reviews each line of code as it is typed in. The two programmers switch roles frequently.

Remember, everyone makes mistakes and that's okay. What's important is learning from them and finding ways to minimize them in the future. And remember, even the best programmers often find that "the problem was a typo" after a long debugging session!
