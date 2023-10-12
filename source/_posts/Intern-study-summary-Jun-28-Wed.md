---
title: Intern study summary - Jun 28 Wed
date: 2023-06-28 20:51:26
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Python,
    Python Grammar,
    Intern At 300K,
    Learning Log,
    Tech,
    List Comprehension,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Key shortcut

My new key board arrived today. I used to use finger combination on the track pad to switch between screens and scroll. Now, I have to use mouse and keyboard. Thus, I will also summarize some of key board shortcut.

---

> Note: I am using MacOS

Switch screen: `Control + Left/Right Arrow`

Scroll horizontally: press `Shift` while scroll with mouse

Switch between tabs in chrome: `cmd + Shift + A`

# Multiple `__init()__` in one class

In Python, a class can only have one `__init__` method. If you define more than one `__init__` method, the last one you define will overwrite the previous ones.

However, there are ways to handle different numbers of parameters while initializing an instance in Python. The most common way is to use default arguments, variable-length arguments or keyword arguments in the `__init__` method.

Here's an example of using default arguments:

```python
class MyClass:
    def __init__(self, a=1, b=2, c=3):
        self.a = a
        self.b = b
        self.c = c

# Create an instance with default parameters
obj1 = MyClass()

# Create an instance with a different set of parameters
obj2 = MyClass(4, 5, 6)

# Create an instance with a different set of parameters, in non-sequential order
obj3 = MyClass(b=7, c=8, a=9)
```

Another way is using variable-length arguments (`*args`) or keyword arguments (`**kwargs`):

```python
class MyClass:
    def __init__(self, *args, **kwargs):
        # args is a tuple of positional arguments
        # kwargs is a dictionary of keyword arguments
        if len(args):
            self.a = args[0]
        if len(args) > 1:
            self.b = args[1]
        if len(args) > 2:
            self.c = args[2]
        if 'd' in kwargs:
            self.d = kwargs['d']

# Using different numbers of positional arguments
obj1 = MyClass(1, 2, 3)

# Using keyword arguments
obj2 = MyClass(d=4)

# Using both positional and keyword arguments
obj3 = MyClass(1, 2, d=4)

```

Remember that using `*args` and `**kwargs` can make your code less clear, because it's not immediately obvious which parameters the class expects. If you know which parameters your class should accept, it's often clearer to use default arguments.

# Operation with YAML file

You may find my introduction to YAML format in my blog: [Intern study summary Jun 15 Thu](https://blog.slray.com/2023/06/23/Intern-study-summary-Jun-15-Thu/#yaml).

---

You can use the `yaml` library in Python to write a dictionary to a YAML file. The `yaml` library is not included in the standard Python library, so you'll need to install it first. You can do so with pip:

```bash
pip install pyyaml
```

Here is an example of how to write a dictionary to a YAML file:

```python
import yaml

# your dictionary
data = {
    "name": "John",
    "age": 30,
    "city": "New York"
}

with open('output.yaml', 'w') as file:
    yaml.dump(data, file)
```

In this example, `yaml.dump()` is used to write the data to the file. The output file ('output.yaml') will look something like this:

```yaml
age: 30
city: New York
name: John
```

Note: YAML is a human-readable data-serialization language. It is commonly used for configuration files and in applications where data is being stored or transmitted.

# List Comprehension

I had list comprehension questions while I was having an interview for a intern position at a quant company. In fact, I did a terrible job on that. Here I will provide two examples for rewrite a for loop in list comprehension form.

## Example 1:

Rewrite:

```python
for pair in top_continuous_pairs:
    symbol = {"exchange": self.exchange, "pairDb": pair}
    symbols.append(symbol)

```

Answer:

```python
symbols = [{"exchange": self.exchange, "pairDb": pair} for pair in top_continuous_pairs]
```

In this single line of code, a new dictionary is created for each item in `top_continuous_pairs`, with "exchange" set to the value of `self.exchange` and "pairDb" set to the current item. The result is a list of these dictionaries, which is assigned to `symbols`.

This is equivalent to the original for-loop, but more concise. Note that list comprehensions can be harder to read than regular for-loops when they start getting complex, so use them wisely.

## Example 2:

Rewrite:

```python
backtest_pairs = []
    for s in symbols:
        backtest_pair = s.copy()
        backtest_pair["channel"] = self.channel
        backtest_pairs.append(backtest_pair)
```

The code above creates a deep copy of each dictionary in `symbols` and then adds a new key-value pair, where "channel" is the key and `self.channel` is the value. After modifying the dictionary, it is then appended to the `backtest_pairs` list.

This for-loop can be rewritten as a list comprehension like so:

```python
backtest_pairs = [{**s, "channel": self.channel} for s in symbols]
```

In this list comprehension, `{**s, "channel": self.channel}` creates a new dictionary which is a copy of `s` with an additional key-value pair where "channel" is the key and `self.channel` is the value. The `{**s}` syntax is using dictionary unpacking to create a copy of the dictionary `s`.

Remember that list comprehensions are great for writing more concise code, but they can also be more difficult to read if they get too complex. Always strive for the right balance between conciseness and readability in your code.

### About the `**s`

Does the `*s` have any relationship with pointer in C?

No, the `**s` in Python does not have any relationship with pointers in C.

In Python, the `**` operator is used for different purposes:

1. When used in a function call, it's used for unpacking dictionary key-value pairs as keyword arguments in a function. For example:

   ```python
   def func(a=1, b=2):
       print(a, b)

   args = {'a': 3, 'b': 4}
   func(**args)  # This will output: 3 4
   ```

   In this case, `**args` is unpacking the dictionary and passing the values to the function as keyword arguments.

2. When used in a function definition, it's used for accepting an arbitrary number of keyword arguments. For example:

   ```python
   def func(**kwargs):
       for key, value in kwargs.items():
           print(f"{key} = {value}")

   func(a=3, b=4, c=5)  # This will output: a = 3, b = 4, c = 5
   ```

   Here, `**kwargs` is capturing all keyword arguments passed to the function into a dictionary.

3. When used with a numeric type, `*` is the exponentiation operator. For example:

   ```python
   print(2 ** 3)  # This will output: 8
   ```

   Here, `**` is used for raising 2 to the power of 3.

In C, the `*` operator is used for a completely different purpose: it's used for creating and dereferencing pointers. Python does not have pointers like C, and the `**` operator in Python does not have any relationship with the pointer concept in C.
