---
title: Intern study summary - Jun 27 Tue
date: 2023-06-28 00:01:48
excerpt: Learning record for what I learned during my internship at 300K—a quant company on the day of 15th of Jun, Thu
tags:
  [
    Intern,
    Intern Log,
    Python,
    Python Grammar,
    Terminal,
    Terminal Command,
    Intern At 300K,
    Learning Log,
    Tech,
    Shell Script Grammar,
    Vim,
    Git,
    Git command,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

# Vim

When I was using git to merge, there was part of the command line popped up asking me to add some commit message. What is that? How can I quit it?

You're very likely to be in Vim, the default editor for many systems, which Git often uses for writing commit messages.

If you want to exit without saving the message, you can follow these steps:

1. Press the `ESC` key (if you are in insert or append mode)
2. Then type `:q!` and press `Enter`. The `:q!` command tells Vim to quit without saving changes.

If you want to save your message and then exit, you can follow these steps:

1. Press `ESC` key (if you are in insert or append mode)
2. Then type `:wq` and press `Enter`. The `:wq` command tells Vim to write (save) the changes and then quit.

## How to change the default editor for git?

You can switch to a different text editor. For example, if you prefer to use visual studio code (vsc), you can switch the default editor for git with the following command:

```
git config --global core.editor "code"
```

# Python add argument

When using Git, you should often use the commend `git commit -m "xxxx"`. Here the `git` is just like running `python xxx.py` or `make`, and the thing following it is called an argument. Here the argument is `commit -m "xxx"`.

You can also do that in python, which means you could do things like `python test.py -times 100`. And, you can make your own argument.

Here is an example:

```python
@click.command()
@click.argument("config", type=str, required=False)
@click.option("-f", "--config-file", type=str, default=None)
def main(config, config_file):
    # Your code here
    pass
```

Explanation:

These lines of code belong to a Python program using the `click` library. `click` is a package for creating beautiful command line interfaces in a composable way. It's highly configurable but comes with sensible defaults out of the box.

Here's the breakdown:

1. **`@click.command()`**: This line is a decorator that tells Click this function will be a command-line command. This means when you run your script from the command-line, this function can be executed.
2. **`@click.argument("config", type=str, required=False)`**: This decorator adds an argument to your command. Arguments are positional parameters to your command. In this case, an argument named `config` is expected, which should be of `str` (string) type. The `required=False` parameter means this argument is optional.
3. **`@click.option("-f", "--config-file", type=str, default=None)`**: This decorator adds an option to your command. Options are extra parameters you can include when calling your command, and they are typically used to modify the behavior of the command in some way. Options are denoted by either a single ``followed by a single character (like`f`) or by `-`followed by a word (like`-config-file`). Either `f`or`-config-file` can be used to specify this option in this case. This option takes a string (`type=str`) and defaults to `None` if it's not provided (`default=None`).

In practical usage, these decorators would be applied to a function that forms the core of your command-line application.

In the code above, `main` is the function that gets executed when you run your command. The parameters `config` and `config_file` get their values from the command-line arguments and options respectively.

## what if I want to have another optional argument other than `config`? Do I add another `@click.arguemnt`?

Yes, you can add additional arguments by using the `@click.argument()` decorator again. The key point to remember is that arguments are positional. This means the order in which they are defined in your function will be the order in which they should be provided when you run your command.

Here's an example of adding an additional argument named `option`:

```python
@click.command()
@click.argument("config", type=str, required=False)
@click.argument("option", type=str, required=False)
@click.option("-f", "--config-file", type=str, default=None)
def main(config, option, config_file):
    # Your code here
    pass
```

In this example, `option` is another optional argument. When running this command, `config` would need to be provided before `option`. For instance:

```bash
python your_script.py my_config my_option -f my_config_file
```

In this command, `my_config` would be passed to the `config` argument, `my_option` would be passed to the `option` argument, and `my_config_file` would be passed to the `config_file` option.

If you find the positional nature of arguments to be limiting or confusing, consider using more options (with `@click.option()`) instead, as options can be provided in any order.

## Does the `@click.option` has a relationship with the upper one. For example, the '-f' is belong to option, I cannot add '-f' after 'config'?

The `-f` or `--config-file` option, as specified in the `@click.option` decorator, can be placed anywhere on the command line when invoking the command, regardless of the order of other arguments or options.

Here are valid ways of calling the command:

```bash
python your_script.py my_config -f my_config_file
python your_script.py -f my_config_file my_config

```

In both of these examples, `my_config` would be passed to the `config` argument and `my_config_file` would be passed to the `config_file` option.

The decorators `@click.argument` and `@click.option` only dictate the structure and syntax of command-line inputs for your Python script, they don't rigidly fix the sequence in which these arguments and options should be passed on the command line. However, do note that the sequence of positional arguments (`@click.argument`) is important while the sequence of options (`@click.option`) is not.

## Here is a more detailed example

Requirement: I want to add an optional argument after `python xx.py` that specify the start, end, and pred date.

For optional parameters like start date, end date, and prediction date, it's usually best to use options (`@click.option`) instead of arguments (`@click.argument`). Options provide more flexibility as they can be specified in any order and they are easier for users to understand because they are more explicit.

Here's how you might add these to your command:

```python
@click.command()
@click.option("--start-date", type=str, help="The start date", default=None)
@click.option("--end-date", type=str, help="The end date", default=None)
@click.option("--pred-date", type=str, help="The prediction date", default=None)
def main(start_date, end_date, pred_date):
    # Your code here
    pass

```

In this example, each option can be used like this:

```bash
python xx.py --start-date 2023-01-01 --end-date 2023-12-31 --pred-date 2024-01-01

```

If a user does not provide an option, its default value will be `None`. If you want a different default value, you can change the `default` parameter in the `@click.option()` call.

As for conventions, it's typical to use hyphens (`-`) to separate words in option and command names, rather than underscores (`_`). This is more consistent with the way other command-line tools work. It's also common to provide a short, one-letter name for each option if it's an option that will be used frequently. This can be done by providing additional names when calling `@click.option()`, like this:

```python
@click.option("-s", "--start-date", type=str, help="The start date", default=None)

```

In this example, the `start-date` option can be specified with either `-s` or `--start-date`. This makes it quicker to type for users who use it frequently, while still keeping it easy to understand for new users.

## Explain the example

why I am using `-` in the decorator but `_` in the main function? Is it just following the order, which means the top argument is the first in the function, or it is corresponding that change '-' to '\_'?

In the Click library, when you define options, they can have a dash in their name (`-`). This is a common practice in command line tools. However, in Python, variable names cannot contain a dash because the dash is interpreted as the subtraction operator. So, in the context of the function definition, you have to use underscores instead of dashes.

Click automatically converts dashes to underscores when passing the values to your function. This allows you to follow command-line conventions when defining your arguments and options, while still having valid Python variable names inside your function.

For example:

```python
@click.option("--start-date", type=str, help="The start date", default=None)
```

Here, the `start-date` will be accessed as `start_date` in your function:

```python
def main(start_date):
    # start_date here is equivalent to --start-date in the option
```

The argument order in the function definition does need to match the order in which you've decorated the function with `@click.argument` and `@click.option` decorators. However, it doesn't matter whether an argument or option has dashes or underscores in its name; Click handles that conversion for you.

---

So if I change

```python
@click.command()
@click.option("--start-date", type=str, help="The start date", default=None)
@click.option("--end-date", type=str, help="The end date", default=None)
@click.option("--pred-date", type=str, help="The prediction date", default=None)
def main(start_date, end_date, pred_date):
    # Your code here
    pass
```

to

```python
@click.command()
@click.option("--start-date", type=str, help="The start date", default=None)
@click.option("--end-date", type=str, help="The end date", default=None)
@click.option("--pred-date", type=str, help="The prediction date", default=None)
def main(start_date, pred_date, end_date):
# Your code here
pass
```

it won't change the functionality of the program?

For options in Click, the order of parameters in your function definition does not have to match the order of the `@click.option()` decorators. This is because options are not positional; they are identified by their names.

So yes, swapping `pred_date` and `end_date` in the function definition won't change the functionality of your program:

```python
@click.command()
@click.option("--start-date", type=str, help="The start date", default=None)
@click.option("--end-date", type=str, help="The end date", default=None)
@click.option("--pred-date", type=str, help="The prediction date", default=None)
def main(start_date, pred_date, end_date):
    # Your code here
    pass

```

You can still call the command with options in any order:

```bash
python xx.py --start-date 2023-01-01 --end-date 2023-12-31 --pred-date 2024-01-01

```

or

```bash
python xx.py --pred-date 2024-01-01 --start-date 2023-01-01 --end-date 2023-12-31
```

Both will work fine.

# Shell script grammar

I am using the shell script grammar under Jenkins, the uppercased string inside of `${}` is a placeholder (a variable that has values, and the string inside `${}` is its name).

## `if-else` statement in shell script grammar example

```bash
if ${PIP_INSTALL}
then
   make install
fi
```

In the code above, `${PIP_INSTALL}` is a boolean typed value. `fi` is an abbreviation for “finish”, meaning the end of the code block. Similar to the `}` in java’s if else statement.

Rewrite the code in Java

```java
if (PIP_INSTALL) {
	// make install execution
}
```

## Check if a variable is empty (similar to `null` in Java and `None` in Python)

If you want to check if a variable in a bash shell script (like the one you posted) is empty, you can use `-z` operator, which returns true if the length of the string is zero.

Here is an example of your script checking if the `${PIP_INSTALL}` variable is empty or not:

```bash
if [ -z "${PIP_INSTALL}" ]
then
   echo "PIP_INSTALL is empty"
else
   make install
fi
```

In this script, `-z "${PIP_INSTALL}"` checks if `${PIP_INSTALL}` is empty. If `${PIP_INSTALL}` is empty, it prints "PIP_INSTALL is empty", otherwise it runs `make install`.

The brackets `[` and `]` are required for the test command (`[ -z "${PIP_INSTALL}" ]` is equivalent to `test -z "${PIP_INSTALL}"`). And also note that there should be a space between `[` and `-z` and between `${PIP_INSTALL}` and `]`.

The `${PIP_INSTALL}` variable is enclosed in quotes. This is a good practice because it prevents word splitting and pathname expansion. If `${PIP_INSTALL}` is not set or if it is set to a value that contains spaces, without the quotes, the script may not behave as expected.

## `AND` operation

If you want to perform an AND operation in a Bash shell script, you can use `-a` operator, or `&&` operator inside your `if` condition.

Let's say you want to check if `${PIP_INSTALL}` is not empty, and some other variable `${SOME_VAR}` equals "yes", you could do something like:

Option 1: Using `-a`:

```
if [ -n "${PIP_INSTALL}" -a "${SOME_VAR}" = "yes" ]
then
   make install
else
   echo "Either PIP_INSTALL is empty or SOME_VAR is not yes"
fi

```

Option 2: Using `&&`:

```
if [ -n "${PIP_INSTALL}" ] && [ "${SOME_VAR}" = "yes" ]
then
   make install
else
   echo "Either PIP_INSTALL is empty or SOME_VAR is not yes"
fi

```

In both scripts:

- `n "${PIP_INSTALL}"` checks if `${PIP_INSTALL}` is not empty.
- `"${SOME_VAR}" = "yes"` checks if `${SOME_VAR}` is equal to "yes".
- The `if` statement is true (and `make install` is executed) if and only if both conditions are true. Otherwise, "Either PIP_INSTALL is empty or SOME_VAR is not yes" is printed.

Again, note the spaces surrounding the brackets and operators. They are required for the script to work correctly.

## equal in if comparing to other languages

If I want to see if something equals to another, unlike other programming languages that I have to write "==", I can use only one "=”.

Yes, that's correct. In Bash shell scripts, you use a single equals sign (`=`) for string comparison in an `if` statement, rather than the double equals sign (`==`) that's typically used in many programming languages.

For example, if you wanted to check if the string variable `${MY_VAR}` equals the string "Hello", you could write:

```bash
if [ "${MY_VAR}" = "Hello" ]
then
   echo "MY_VAR is Hello"
else
   echo "MY_VAR is not Hello"
fi
```

This would print "MY_VAR is Hello" if `${MY_VAR}` equals "Hello", and "MY_VAR is not Hello" otherwise.

However, it is also worth noting that using `==` for comparison also works in bash and it is common to see it in many scripts for the sake of clarity and consistency with other programming languages, although `=` is more traditional:

```bash
if [ "${MY_VAR}" == "Hello" ]
then
   echo "MY_VAR is Hello"
else
   echo "MY_VAR is not Hello"
fi
```

In both cases, be sure to leave spaces around the `=` or `==`. Bash treats them as special characters and requires spaces around them to correctly interpret your command.

## “`print`” in shell

The `echo` in the code above, then, is like the print statement in other language?

Yes, that's correct. The `echo` command in Bash is similar to the `print` statement in many other programming languages.

`echo` is used in shell scripts and command line interfaces to output the strings it is being passed as arguments to the standard output, which is typically the terminal. This can be text or the value of a variable.

For example:

```bash
echo "Hello, World!"
```

This command will print "Hello, World!" to the terminal.

And you can use it with variables like this:

```bash
MY_VAR="Hello, World!"
echo ${MY_VAR}
```

This will also print "Hello, World!" to the terminal.
