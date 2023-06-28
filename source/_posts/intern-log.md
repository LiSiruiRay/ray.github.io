---
title: Intern study summary - Jun 13 Tue
date: 2023-06-14 01:38:25
excerpt: Learning record for what I learned during my internship at 300K—a quant company.
tags:
  [
    Intern,
    Intern Log,
    Python,
    Python Grammar,
    Git,
    Git Command,
    Terminal,
    Terminal Command,
    Intern At 300K,
    Learning Log,
    Tech,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Python

## `pass`

In Python, the `pass` keyword is a **placeholder** statement. It is used when a statement is required syntactically, but no action needs to be performed.

### Example.

Suppose you're designing a new class and you know it needs a particular method, but you aren't sure what the implementation of that method should be yet. You can use `pass` as a placeholder like so:

```python
class MyClass:
    def future_method(self):
        pass
```

In this code, `future_method` does nothing, but it's a valid method. Without the `pass` statement, the code would have a syntax error because Python expects a block of code to follow the method definition. The `pass` keyword is saying, "I acknowledge that you expect something here, but I don't want to do anything."

Similarly, it can be used in conditional statements, loops, etc. where some code is expected:

```python
if some_condition:
    pass  # TODO: handle this condition

```

In the above case, `pass` is used to indicate that this condition should be handled in the future.

Keep in mind, it's typically used in development phases, and in most cases, you should eventually replace `pass` with some meaningful code.

## `@dataclass`

The `@dataclass` decorator is a feature of Python introduced in Python 3.7, as part of the `dataclasses` module. It provides a convenient way to generate special methods for classes which mainly contain data, like the **init**, **repr**, and **eq** methods.

### Example:

```python
@dataclass
class DogInfo():
    name: str
    age: int

```

`DogInfo` is defined as a dataclass, which means Python will automatically add special methods like **init**, **repr**, and **eq**.

### Compare to Java

Java does not have a direct equivalent to Python's dataclasses, but it does have similar features for creating simple classes that mainly contain data.

One approach in Java is to create a simple class with private fields, and generate getter, setter, `equals()`, `hashCode()`, and `toString()` methods. Most modern Java IDEs can generate these methods for you.

Starting from Java 14, there's also a preview feature called `record` which provides similar functionality to Python's dataclasses. Here's an example:

```java
public record DogInfo(String name, int age) {}
```

In this Java code, `DogInfo` is defined as a record with two fields: `name` and `age`. Java automatically generates constructor, equals, hashCode, and toString methods.

### `@dataclass(frozen=True)` the `(frozen=True)`

The `@dataclass` decorator in Python comes with several parameters that you can use to customize the behavior of the dataclass, and one of these parameters is `frozen`.

If you set `frozen=True` when you define a dataclass, it makes the instances of that dataclass immutable, similar to a tuple. That is, once the instance has been created and its attributes have been set, you can't change them.

If you try to change an attribute of a frozen dataclass, Python raises an exception. Here's an example:

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(3, 4)
p.x = 5  # Raises a `dataclasses.FrozenInstanceError`

```

If you run this code, you'll get a `FrozenInstanceError` when you try to assign `5` to `p.x`.

The `frozen=True` parameter is useful when you want to create a dataclass that is hashable and can be used as a dictionary key, or when you want to ensure that an instance of the class won't change once it's been created.

#### Compare to Java’s `final` keyword

Compare with `final float PI` in Java: the `frozen=True` parameter for Python's `@dataclass` and the `final` keyword in Java serve similar purposes, but they're not exactly the same.

The `frozen=True` parameter in Python makes it so that all attributes of an instance of a dataclass are immutable -- you can't change them once they're set. This is similar to how a `final` variable in Java can't be changed once it's initialized.

However, the `final` keyword in Java can be used in several contexts. When used with a variable (like `final float PI`), it makes that variable a constant -- you can't change its value once it's initialized. When used with a class, it means the class can't be subclassed. When used with a method, it means the method can't be overridden by subclasses.

The `frozen=True` parameter in Python's `@dataclass` only affects the mutability of instances of the class. It doesn't prevent the class from being subclassed, nor does it prevent methods from being overridden. There's no exact equivalent in Python for Java's `final` keyword when used with classes and methods. Python's philosophy tends towards allowing greater flexibility, so "final" classes and methods are not a language feature.

### `post_init`

The `__post_init__` method in Python is a special method provided by the dataclasses module. It's a method that you can define in your dataclass, and if it exists, it gets called automatically at the end of the generated `__init__` method.

The purpose of the `__post_init__` method is to perform any additional initialization steps that you need to do beyond just setting the attributes of your dataclass to the provided values.

Here's an example of how you might use it:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

    def __post_init__(self):
        self.magnitude = (self.x ** 2 + self.y ** 2) ** 0.5

p = Point(3, 4)
print(p.magnitude)  # Outputs: 5.0

```

In this example, after the `x` and `y` attributes are set in the `__init__` method, the `__post_init__` method is automatically called. This calculates the magnitude of the point from its `x` and `y` coordinates and stores it in the `magnitude` attribute.

If you don't define a `__post_init__` method in your dataclass, then nothing extra happens after `__init__`. But if you do define it, then it provides a way to add some additional initialization behavior that goes beyond just setting attribute values.

## Inherit in Python and Java

Inheritance is a key feature in many object-oriented programming languages, including Python and Java. It is a mechanism that allows a class to acquire the properties and behaviors of another class.

The class from which properties are inherited is known as the parent class, superclass, or base class. The class that inherits those properties is known as the child class, subclass, or derived class.

When a class inherits from another class in Python, it gains access to all of the superclass's attributes and methods. This means that objects of the subclass can be treated as instances of the superclass, and code that works with the superclass will automatically work with the subclass.

Python's inheritance is defined as follows:

```python
class ParentClass:
    pass

class ChildClass(ParentClass):
    pass
```

In this example, `ChildClass` is inheriting from `ParentClass`.

Python supports both single and multiple inheritance. Single inheritance is when a class inherits from a single superclass, while multiple inheritance is when a class inherits from more than one superclass. Here is an example of multiple inheritance:

```python
class ParentClass1:
    pass

class ParentClass2:
    pass

class ChildClass(ParentClass1, ParentClass2):
    pass
```

In this example, `ChildClass` is inheriting from both `ParentClass1` and `ParentClass2`.

Java also supports inheritance, but with a key difference: Java does not support multiple inheritance for classes. A class in Java can only directly inherit from one superclass. However, a class can implement multiple interfaces, which is a sort of workaround for multiple inheritance.

This is how you define inheritance in Java:

```java
public class ParentClass {
}

public class ChildClass extends ParentClass {
}
```

In this example, `ChildClass` is inheriting from `ParentClass`.

Despite the difference in multiple inheritance, the fundamental idea behind inheritance is the same in both languages. It's a way to organize code, increase reusability, and set up relationships between different types.

Also, Java have abstract class which might be different with what python’s inherit behaves.

#### A key example:

Yes, you're correct. When you create an instance of `AClass` by calling `AClass("t1", "t2", "t3")`, the `p1`, `p2`, and `p3` attributes of `a_class` will be set to "t1", "t2", and "t3" respectively.

The values "p1_attribute", "p2_attribute", and "p3_attribute" specified in the class definition are default values. They are used if you create an instance without providing corresponding arguments.

Here's how it works:

Try to answer the following question:

If I have code

```python
@dataclass(frozen=True)
class AClass:
    p1: str = "p1_attribute"
    p2: str = "p2_attribute"
    p3: str = "p3_attribute"
```

and I have `a_class = AClass("t1", "t2", "t3")`, then the `p1, p2, p3` of `a_class` will be "t1", "t2", and "t3"?

<style>
.tog-button {
    color: white;
  border-radius: 20px; /* Rounded corners */
  /* border: 10px;  */
  /* Remove default button border */
  /* border-color: white; */
  padding: 10px 20px; /* Add some padding */
  text-decoration: none; /* Remove default underline */
  cursor: pointer; /* Change cursor on hover */
  background-color: transparent; /* Transparent background */
  color: white; /* Add some color */
}

.tog-button:hover {
  background-color: #ddd; /* Add a hover effect */
  color: black;
}

.tog-content {
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 5px;
    /* color: white; */
    /* boarder: 10px; */
}
</style>
<div class="tog-container">
  <button class="tog-button">Show/Hide Answer</button>
</div>

<div class="tog-content" style="display: none;">
Yes, it’s correct. When you create an instance of `AClass` by calling `AClass("t1", "t2", "t3")`, the `p1`, `p2`, and `p3` attributes of `a_class` will be set to "t1", "t2", and "t3" respectively.
The values "p1_attribute", "p2_attribute", and "p3_attribute" specified in the class definition are default values. They are used if you create an instance without providing corresponding arguments.
Here's how it works:

```python
from dataclasses import dataclass
@dataclass(frozen=True)
class AClass:
p1: str = "p1_attribute"
p2: str = "p2_attribute"
p3: str = "p3_attribute"
# Creating an instance with arguments
a_class = AClass("t1", "t2", "t3")
print(a_class.p1)  # Outputs: t1
print(a_class.p2)  # Outputs: t2
print(a_class.p3)  # Outputs: t3
# Creating an instance without arguments
another_class = AClass()
print(another_class.p1)  # Outputs: p1_attribute
print(another_class.p2)  # Outputs: p2_attribute
print(another_class.p3)  # Outputs: p3_attribute
```

In this code, when we create `a_class` with arguments, those arguments override the default values. But when we create `another_class` without arguments, the attributes get set to the default values.

</div>

<script>
let togButton = document.querySelector('.tog-button');
let togContent = document.querySelector('.tog-content');

togButton.addEventListener('click', function() {
    console.log("clicked")
  if(togContent.style.display == 'none') {
    togContent.style.display = 'block';
  } else {
    togContent.style.display = 'none';
  }
});
</script>

## `yield`

I search several times on the `yiedl` key word in python. Even with ChatGPT, I cannot clearly understand how `yield` keyword is working in python and why we need it. One key step to understand it is that the **`yield`** keyword cannot be replaced with **`return`** because **`return`** in a function terminates the function entirely, while **`yield`** only pauses the function. If you replace **`yield`** with **`return`**, your function will stop executing after the first **`return`** statement it encounters, and it will not be able to produce multiple values over time.

---

The `yield` keyword in Python is used in a special kind of function called a generator. Generators are a type of iterable, like lists or tuples. Unlike lists, they don't allow indexing with arbitrary indices, but they can still be iterated through with for loops.

When the `yield` keyword is called in a function, it returns a value and pauses execution of the function. The function can then be resumed right where it left off, allowing it to generate a sequence of results over time, instead of computing them all at once and returning them in a list for instance.

Here's a simple example:

```python
def simple_generator():
    yield 1
    yield 2
    yield 3

for value in simple_generator():
    print(value)
```

This will output:

```python
1
2
3
```

If you only need to generate a single value from your function, then you can use `return` instead of `yield`. Also, if your function generates a small number of values, you might be able to use `return` to return them all at once in a list, rather than generating them one at a time with `yield`.

In summary, `yield` is a special keyword used in the context of Python generators, and it cannot be simply replaced with `return` without changing the function's behavior.

### Can I use a block of code to implement `yield`?

In Python, the `yield` keyword is used to make a function into a generator that can produce a sequence of results over time. However, the `yield` keyword itself does quite a bit of behind-the-scenes work, so it's not straightforward to replace it with a few lines of code. You would need to implement a whole class with an iterator to get equivalent behavior.

Here is a simple example of how you might do that. This example implements a generator that yields the numbers 1, 2, and 3, similar to the previous example.

```python
class SimpleGenerator:
    def __init__(self):
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        self.current += 1
        if self.current > 3:
            raise StopIteration
        else:
            return self.current

# Use the generator
for value in SimpleGenerator():
    print(value)

```

In this code, the `SimpleGenerator` class has a `__next__` method that implements the logic of the generator: it keeps track of the current number, and each time `__next__` is called, it increments the number and returns it. When the number gets bigger than 3, it raises a `StopIteration` exception, which signals that the generator is done.

This is a more explicit way of implementing a generator, and it can give you a clearer sense of what's going on behind the scenes when you use the `yield` keyword. However, using `yield` is generally simpler and more concise, which is why it's usually preferred for creating generators in Python.

### but with `yield` keyword, there won't be such thing to store the iterable result, which means I won't be able to print the whole return result inside of the method with 'yield`?

<div class="tog-container">
  <button class="tog-button" name="button2">Show/Hide Answer</button>
</div>

<div class="tog-content" style="display: none;" name="content2">
You're correct. A generator (a function that uses the `yield` keyword) doesn't return a value that can be stored in a variable like a list or tuple. Instead, it produces values one at a time on-the-fly as you iterate over the generator.
  Here's an example:

```
def simple_generator():
    yield 1
    yield 2
    yield 3

# This won't work as you might expect:
result = simple_generator()
print(result)  # Outputs: <generator object simple_generator at 0x7f...>

```

When you try to print `result`, it tells you that it's a generator object, not a list of numbers.
If you want to collect all the values produced by a generator into a list, you can do that by passing the generator to the `list` function:

```
result = list(simple_generator())
print(result)  # Outputs: [1, 2, 3]

```

However, it's important to remember that one of the main reasons to use generators is when you're working with a large amount of data that won't fit into memory all at once. In such cases, you'd generally want to process the generator's values one at a time, rather than trying to store them all in a list.

</div>

<script>
let elements = document.getElementsByName('button2');
let button2 = elements[0]
let elements2 = document.getElementsByName('content2');
let content2 = elements2[0]

button2.addEventListener('click', function() {
    console.log("clicked")
    console.log(content2)
  if(content2.style.display == 'none') {
    content2.style.display = 'block';
  } else {
    content2.style.display = 'none';
  }
});
</script>

## `enumerate()`

The `enumerate` function in Python is a built-in function that allows you to loop over something and have an automatic counter.

In a `for` loop, when you use `enumerate(sources)`, it returns an enumerate object, which is an iterable that yields pairs of the form `(index, value)`. In each iteration of the loop, `idx` is the index of the current item, and `source` is the value of the current item.

Here's a simple example:

```python
fruits = ['apple', 'banana', 'cherry']

for idx, fruit in enumerate(fruits):
    print(idx, fruit)
```

This will output:

```python
0 apple
1 banana
2 cherry
```

As you can see, `idx` is the index of each item in the list, and `fruit` is the value of each item. This can be very useful in situations where you need to know the index of the items as you're looping over them.

## `iter()`

The `iter()` function in Python is a built-in function that returns an iterator object. An iterator is an object that can be iterated (looped) upon. An object which will return data, one element at a time when `next()` is called on it.

In more technical terms, an iterator is an object that implements the iterator protocol, which consists of the methods `__iter__()` and `__next__()`.

Here's an example:

```python
my_list = [1, 2, 3, 4]
my_iter = iter(my_list)

print(next(my_iter))  # Outputs: 1
print(next(my_iter))  # Outputs: 2
print(next(my_iter))  # Outputs: 3
print(next(my_iter))  # Outputs: 4

```

In this code, `iter(my_list)` returns an iterator object that you can fetch values from one at a time using the `next()` function. Once all elements are exhausted, `next()` will raise a `StopIteration` exception.

Most of the time, you don't need to use `iter()` and `next()` directly, because Python's `for` loop automatically calls them when you loop over an iterable object:

```python
my_list = [1, 2, 3, 4]
for element in my_list:
    print(element)

```

This code does the same thing as the previous example, but it's a lot simpler and clearer. The `for` loop automatically calls `iter(my_list)` to get an iterator, and then it calls `next()` on that iterator for each iteration of the loop. When `next()` raises a `StopIteration` exception, the `for` loop catches it and gracefully exits the loop.

## ternary operator in Python

### Example:

rewrite the following Java code in Python

```java
int j = 5;
int i = (j > 1) ? 6 : 7; // if j > 1, then i = 6, else, i = 7
```

Python does have a similar construct, but the syntax is different from Java. Instead of `(j > 1) ? 6 : 7`, in Python you would write `6 if j > 1 else 7`.

Here's an example:

```java
j = 5
i = 6 if j > 1 else 7
```

In this Python code, `6 if j > 1 else 7` is a conditional expression (often called a ternary operation). It checks if `j > 1`. If `j > 1` is true, it evaluates to `6`. Otherwise, it evaluates to `7`. This value is then assigned to `i`.

This is similar to the `? :` operator in Java, but the syntax is slightly different.

# Git

## Differences between `git switch another-branch` and `git checkout another-branch`

In versions of Git prior to 2.23, the command `git checkout` was primarily used to switch between branches in a Git repository. However, starting from Git 2.23, the command `git switch` was introduced as a more intuitive and safer alternative to `git checkout` when it comes to switching branches. Here are the main differences between the two commands:

1. Simplicity and clarity: The `git switch` command was introduced to provide a clearer and more straightforward syntax for switching branches. The name "switch" better reflects the action being performed, making it easier to understand its purpose at a glance.
2. Safety: The `git switch` command is designed to be more cautious and less error-prone than `git checkout`. When using `git switch`, Git performs additional checks to ensure that you don't accidentally lose work or overwrite uncommitted changes. For example, if you have uncommitted changes in your working directory, Git will refuse to switch branches unless you either commit the changes or stash them. This helps prevent data loss.
3. Branch creation: With `git switch`, you can create and switch to a new branch in a single command using the `git switch -c <new-branch>` syntax. This combines the creation of a branch and switching to it into a single step. In contrast, `git checkout` requires separate commands to create and switch to a new branch (`git branch <new-branch>` and `git checkout <new-branch>`).
4. Replacing detached HEAD: In `git switch`, the command `git switch <commit>` is used to switch to a specific commit, effectively replacing the old "detached HEAD" state. This syntax makes it more explicit and less error-prone to switch to a specific commit, as opposed to `git checkout <commit>`.

It's worth noting that `git checkout` is still supported in recent versions of Git to maintain backward compatibility, but `git switch` is encouraged for branch switching operations due to its advantages in safety and simplicity.

## Git command for create and checkout to a new branch together

You may use `git checkout -b another-branch`

# Terminal Command

## Split the command window to multiple sub windows

You may use `command` + `D` on key board
