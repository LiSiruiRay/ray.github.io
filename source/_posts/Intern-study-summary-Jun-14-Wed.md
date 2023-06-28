---
title: Intern study summary - Jun 14 Wed
date: 2023-06-14 22:25:45
excerpt: Learning record for what I learned during my internship at 300K—a quant company.
tags:
  [
    Intern,
    Intern Log,
    Python,
    Python Grammar,
    PyCharm,
    PyCharm Professional,
    Terminal,
    Terminal Command,
    Intern At 300K,
    Learning Log,
    Hexo,
    HTML,
    Tech,
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---

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

# Python

## Polymorphism

### Does python have the same thing as Java's Polymorphism? For example, I have `class Car: price: int; age: int; name: str`and `class BMW(car): power: str; color: str`, when I declare a function as `get_car_info(car: Car)` can I input an instance of `BMW`? will i be able to access the `power` and `color` attribute of the input, which is type `Car`?

<div class="tog-container">
  <button class="tog-button">Show/Hide Answer</button>
</div>

<div class="tog-content" style="display: none;">
Yes, Python does support a form of polymorphism, but it is somewhat different from Java's. Python's typing is dynamic, whereas Java's is static. In Python, if an object has a method or property, you can use it, regardless of the object's type. This concept is often summarized by the phrase "duck typing": If it walks like a duck and quacks like a duck, it's a duck.

In your example, you define `Car` and `BMW` classes, and then a function `get_car_info(car: Car)`. The type hint `Car` is just that: a hint. Python will allow you to pass any object to `get_car_info`. If the object has all the properties and methods the function tries to use, the function will work fine.

So, yes, you can pass a `BMW` instance to `get_car_info`. Whether you can access `power` and `color` depends on the body of `get_car_info`. If the function tries to access those properties, it will work fine with a `BMW` instance. If you pass in a `Car` instance that doesn't have those properties, however, the function will raise an AttributeError.

Here is an example:

```
class Car:
    def __init__(self, price: int, age: int, name: str):
        self.price = price
        self.age = age
        self.name = name

class BMW(Car):
    def __init__(self, price: int, age: int, name: str, power: str, color: str):
        super().__init__(price, age, name)
        self.power = power
        self.color = color

def get_car_info(car: Car):
    print(f'Name: {car.name}, Age: {car.age}, Price: {car.price}')
    if isinstance(car, BMW):
        print(f'Power: {car.power}, Color: {car.color}')

my_bmw = BMW(30000, 3, 'BMW X5', '250 hp', 'Red')
get_car_info(my_bmw)

```

Here, the `get_car_info` function first checks whether the `car` is an instance of `BMW` before trying to access the `power` and `color` attributes, avoiding an AttributeError when used with a `Car` instance.

It's worth mentioning that Python's approach to polymorphism is more flexible than Java's, but it also has downsides. The lack of strict typing can lead to errors that are only caught at runtime, rather than at compile-time like in Java. You might want to consider using Python's optional type checking features to get some of the benefits of static typing while keeping the flexibility of Python.

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

# pyenv

## Use pyenv to change the python version of your system (global)

`pyenv` is a simple yet powerful Python version management tool. Here are the steps to change the global Python version using `pyenv`:

1. **Install `pyenv`**: Before you use `pyenv`, you need to install it. Here is the command to install `pyenv` using Homebrew on macOS:

   ```bash
   brew update
   brew install pyenv
   ```

   For Linux, you can use the `pyenv-installer`:

   ```bash
   curl <https://pyenv.run> | bash
   ```

   Make sure you follow the instructions provided by the installer to set up your shell for pyenv usage.

2. **List Available Python Versions**: Use the `pyenv install --list` command to see the list of all available Python versions. It should display a list of versions like this:

   ```bash
   2.7.17
   3.5.9
   3.6.10
   3.7.7
   3.8.2
   ...
   ```

3. **Install a Specific Python Version**: You can install a specific Python version using `pyenv`. For example, to install Python 3.8.2, you would use this command:

   ```
   pyenv install 3.8.2
   ```

4. **Set the Global Python Version**: After the Python version you want is installed, you can set it as the global version with `pyenv global`. For example, to set Python 3.8.2 as the global version, you would use this command:

   ```
   pyenv global 3.8.2
   ```

5. **Confirm the Change**: To confirm that the global Python version has been changed, you can use the `python --version` command. It should display something like `Python 3.8.2`, indicating that the version has been changed.

That's it! You have successfully changed the global Python version using `pyenv`. Keep in mind that changing the global Python version will affect all the Python programs on your system, so make sure you really want to do this before you proceed.

## Won’t work

Sometimes you will find that even you followed the steps aforementioned, when you type `python3 --version`, you will still see the old version, which means the command `pyenv global 3.8.2`is not working. Keep reading:

If you have just installed `pyenv` and have set the global Python version, but when running `python3 --version` the version shown is still the system version (Python 3.8.9 in this case), it suggests that your shell environment might not be set up properly to use `pyenv`.

One of the common reasons for this is that `pyenv` is not properly integrated in your shell.

Here is how you can do it:

1. **Add `pyenv` to bash so command line knows about `pyenv`**: Depending on your shell, you need to add these lines to your shell's configuration file (`.bashrc` or `.bash_profile` for bash, `.zshrc` for Zsh):

   For `bash`:

   ```
   echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\\n  eval "$(pyenv init -)"\\nfi' >> ~/.bashrc
   source ~/.bashrc

   ```

   For `zsh`:

   ```
   echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\\n  eval "$(pyenv init -)"\\nfi' >> ~/.zshrc
   source ~/.zshrc

   ```

   These commands will append the `pyenv init` command to your shell configuration file and then reload the shell configuration file. The `pyenv init` command configures the shell to use `pyenv` for Python version management.

2. **Restart your shell**: This ensures that the changes take effect.
3. **Check the Python version**: Now, when you type `python3 --version`, it should display `Python 3.8.16` or whatever version you set as the global version using `pyenv`.

If the version is still not showing up correctly, it might be due to the specific naming of the python binaries. Try using the command `python --version` instead of `python3 --version`. `pyenv` might have set the version correctly, but the `python3` command could still be pointing to your system's Python installation.

If the issue persists, do check the list of Python versions installed under `pyenv` using `pyenv versions`. This will help you verify whether the intended Python version is correctly installed under `pyenv`. If it's not listed, you'll need to install it using `pyenv install 3.8.16`.

# PyCharm:

Note: I am using the newest version up to date 2023 Jun 14th of PyCharm Professional. (MacOS with M1 Pro)

## Install a python package in PyCharm

Installing a Python package in PyCharm is quite simple. Here's how you do it:

1. Open your project in PyCharm.
2. Navigate to `File -> Settings`. For macOS, navigate to `PyCharm -> Preferences`.
3. In the Settings/Preferences dialog, navigate to `Project: [your_project_name] -> Project Interpreter`.
4. In the top-right corner of the dialog, click the `+` (plus) icon to add a new package.
5. In the new window that opens (the Available Packages dialog), you'll see a list of available packages. You can search for the package you want to install by typing its name into the search box.
6. After you find your package, simply click the `Install Package` button at the bottom of the dialog.
7. Wait for PyCharm to download and install the package. You can see the installation progress in the status bar at the bottom of the dialog.
8. After the installation finishes, you can close the dialog. Your package is now installed and ready to use.

Remember that PyCharm creates a new virtual environment for each project by default, so the package you've installed is only available in the current project's environment. If you want the package to be available globally or in a different project, you need to install it in the corresponding environment.

# Command

## Clear the terminal:

You can simply type the following code to clear your screen:

```bash
clear
```

Yet you will find that when you scroll up, the content you typed before still exists. Here is one of the reasons:

The `clear` command on a Unix-based system like macOS will clear the visible window of the terminal, but it doesn't clear the terminal's scrollback buffer. That means when you scroll up, you'll still be able to see the previous commands and output.

If you want to clear the terminal completely, including the scrollback buffer, you can use the `reset` command:

```bash
reset
```

This will not only clear the terminal screen, but also the scrollback buffer, so you won't be able to scroll up to see previous output.

Alternatively, if you're using the Terminal app on macOS, you can use the keyboard shortcut `Cmd+K` to clear the screen and the scrollback buffer. This works in both bash and zsh, the default shell on macOS as of Catalina.

Please note, always be careful when using commands that permanently delete history or output, especially in production environments or when working with sensitive data.

Note: sometimes when you use `reset`command, the effect is same as using `clear` . In this case, `Cmd+K` is a better choice.

# Hexo

I am using Hexo basing my blog. I also had some tips for how to further beautify your blog.

Note, you might know that Hexo could render Markdown grammar. Yet you might not know that Hexo could also render html code in the middle of Markdown file. I used to write my blog in notion, yet notion, unlike classical markdown compiler, supported tog element which allows you to hide and show content when other people click on a button. Here is a html version of code you can use to achieve the same effect. Actually, you’ve seen such use at the beginning of this blog.

Here's the way to add the HTML and JavaScript code into your Markdown file:

```html
Here's an interactive section that hides and shows content:

<div class="tog-container">
  <button class="tog-button">Show/Hide Answer</button>
  <div class="tog-content" style="display: none;">
    <p>Your answer goes here.</p>
  </div>
</div>

<script>
  let togButton = document.querySelector(".tog-button");
  let togContent = document.querySelector(".tog-content");

  togButton.addEventListener("click", function () {
    if (togContent.style.display == "none") {
      togContent.style.display = "block";
    } else {
      togContent.style.display = "none";
    }
  });
</script>
```

Please note that in the above example, the JavaScript code is embedded directly in the Markdown file. Some Markdown processors have safety features that remove script tags. If your script is not running after you add it, you might want to check your Markdown processor's documentation or use an external `.js` file.
