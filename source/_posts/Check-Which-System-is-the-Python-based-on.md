---
title: Check Which System is the Python based on
date: 2024-01-10 17:11:17
excerpt: Check whether your python is x86 or arm
tags:
---

When installing some libraries in Python, I sometimes encounter the problem of mis installing the version of python. Sometimes I need a library on my M1 Pro chip but my conda installed x86 version. Here are some method to check which python you installed.

# Check if the python installed is x86 or arm

To determine whether the Python installation on your system is x86 (32-bit or 64-bit Intel architecture) or ARM (architecture used in many modern mobile devices and the Apple M1/M2 chips), you can use Python itself to check. Follow these steps:

1. **Open Python Interpreter**: Open a terminal or command prompt and type `python` or `python3`, then press Enter. This will open the Python interpreter.

2. **Check System Architecture**: In the Python interpreter, enter the following commands:

   ```python
   import platform
   print(platform.machine())
   ```

This command will output the architecture type of your Python installation. Here's what the outputs generally mean:

- **Outputs like 'x86', 'x86_64', 'AMD64'**: These indicate an x86 architecture (32-bit or 64-bit Intel or AMD processors).
- **Outputs like 'arm64', 'aarch64'**: These indicate an ARM architecture (common in modern mobile devices, and Apple's M1/M2 series chips).

Let's run this code in the current Python environment to demonstrate.

The output from the Python environment here is `'x86_64'`, which indicates that this Python installation is running on an x86 architecture, specifically a 64-bit Intel or AMD processor.

You can use the same method on your system to check whether your Python installation is x86 or ARM.

# Is there a command line version?

Yes, there is a command-line method to check the architecture of the Python installation without entering the Python interpreter. You can use the following command in your terminal or command prompt:

```bash
python -c "import platform; print(platform.machine())"
```

or

```bash
python3 -c "import platform; print(platform.machine())"
```

This command executes a short Python script directly from the command line. It imports the `platform` module and prints out the machine type, which indicates the architecture.

- If the output is something like `'x86'`, `'x86_64'`, or `'AMD64'`, it's an x86 architecture.
- If it says `'arm64'`, `'aarch64'`, or similar, it's an ARM architecture.

You should choose `python` or `python3` based on how Python is installed on your system. Some systems may use `python` for Python 2 and `python3` for Python 3, while others might only have Python 3 installed as `python`.
