---
title: Intern study summary - Jul 11 Tue
date: 2023-07-11 23:06:41
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Learning Log,
    Intern At 300K,
    Python,
    Python Grammar,
    Command Line,
    Programming Tips,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Python

## Equal and `==`

`self.assertEqual` is a method provided by the `unittest` module in Python. It is used in test cases to assert that two values are equal. If they are not, the test fails and an AssertionError is raised.

`self.assertEqual` uses the `==` operator to check for equality, which checks if two objects have the same value. This can be thought of as checking equality "by value".

In Python, `==` compares the value of two objects, whereas the `is` operator checks whether they are the exact same object (i.e., comparing "by reference"). For instance:

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)  # True - a and b have the same values
print(a is b)  # False - a and b are different objects in memory
print(a is c)  # True - a and c are the exact same object in memory

```

### compare with Java

In Java, the `==` operator checks for reference equality (whether two variables reference the exact same object), which is analogous to the `is` operator in Python. The `equals` method in Java is used to check for value equality, but its behavior can vary depending on how it's implemented in the class of the objects being compared. This is most similar to the `==` operator in Python or `self.assertEqual` in Python's `unittest` module.

## Return two different type together

### example:

If I have return as `return model, target_size`, `model` is `Model` type, and `target_size` is ndarray type. How do I put in the function after `>`?

The "->" symbol is used in Python type hinting to indicate the return type of a function. It's used to make the code easier to understand and maintain, and to help in using tools that perform type checking or provide coding assistance.

In your case, since your function is returning a tuple of a `Model` type object and a `ndarray` type object, you can indicate the return type like this:

```python
from keras.models import Model
import numpy as np

def your_function(...) -> tuple[Model, np.ndarray]:
    # your code
    return model, target_size

```

In this code:

- `Model` is the type for the `model`.
- `np.ndarray` is the type for `target_size`.

Note that these type hints are not enforced at runtime. Python is still a dynamically typed language. These hints are primarily for the benefit of the programmer and development tools.

Also, if your Python version is lower than 3.9, you might want to use `Tuple` from `typing` module because the built-in `tuple` does not support type hints in those versions:

```python
from keras.models import Model
import numpy as np
from typing import Tuple

def your_function(...) -> Tuple[Model, np.ndarray]:
    # your code
    return model, target_size

```

# Database

## MongoDB Read by ID

In order to access MongoDB data in Python, you need to use a library called `pymongo`. Here's a simple example of how you can retrieve a document by its `_id`:

Firstly, install the `pymongo` package using pip:

```python
pip install pymongo

```

And then use this script:

```python
from pymongo import MongoClient
from bson.objectid import ObjectId

# Create a client connection to your MongoDB instance
client = MongoClient('mongodb://localhost:27017/')

# Access the 'database' database and the 'collection' collection
db = client['database']
collection = db['collection']

# Define the id you're looking for
id_to_find = '60ebe5ab9d6965cb97f9d0a2'  # replace with your id string

# Use the ObjectId class to convert the id string into an ObjectId
id_to_find = ObjectId(id_to_find)

# Use the find_one method, which returns the first matching document
document = collection.find_one({'_id': id_to_find})

# Print the document
print(document)

```

You will need to replace `'mongodb://localhost:27017/'` with the connection string for your own MongoDB instance. Replace `'database'` and `'collection'` with the names of your database and collection, and `'60ebe5ab9d6965cb97f9d0a2'` with the id string of the document you're trying to find.

This script first connects to your MongoDB instance and selects the desired database and collection. It then uses the `find_one` method on the collection to retrieve the document with the specified `_id`. The `find_one` method returns `None` if no document matches the query. Finally, it prints out the document.

Remember to import the `ObjectId` class from the `bson.objectid` module, which is needed to convert the string representation of an id into a proper MongoDB ObjectId.
