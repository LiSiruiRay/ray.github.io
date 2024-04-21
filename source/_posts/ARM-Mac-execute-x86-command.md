---
title: ARM Mac execute x86 command
date: 2024-01-10 17:22:42
excerpt: Check and switch between ARM and x86 command
tags:
---

# How can I make my mac (M1 Pro chip) to execute command under x86?

To run x86 commands or applications on a Mac with an M1 Pro chip, which uses Apple's ARM-based architecture, you can use the Rosetta 2 translation environment. Rosetta 2 enables apps built for Intel x86 architecture to run on Apple Silicon Macs. Here's how you can set this up and use it:

1. **Install Rosetta 2 (if not already installed):**

   - Open Terminal.
   - Type `/usr/sbin/softwareupdate --install-rosetta --agree-to-license` and hit Enter.
   - This command will download and install Rosetta 2 on your Mac.

2. **Run x86 Terminal Applications:**

   - For one-time use of an x86 command, precede your command with `arch -x86_64`. For example: `arch -x86_64 <command>`.
   - This tells the system to run the command using the x86_64 architecture.

3. **Set Up an x86 Terminal Environment:**

   - If you regularly use x86 applications, you can duplicate the Terminal app and set the duplicate to always open in Rosetta mode.
   - To do this, find Terminal in Finder (usually in the Utilities folder within Applications), right-click, and choose `Duplicate`.
   - Right-click on the duplicate, choose `Get Info`, and then select the `Open using Rosetta` checkbox.
   - Now, whenever you open this duplicated Terminal app, it will run in x86 mode by default.

4. **Installing x86 Versions of Software:**

   - Some software might require you to specifically download the x86 version. This is typically noted on the software's download page.
   - Install the software as usual. Rosetta 2 will automatically handle the translation.

5. **Limitations:**

   - While Rosetta 2 is quite powerful, there might be some performance overhead compared to running natively on Apple Silicon.
   - Not all x86 applications are guaranteed to work flawlessly under Rosetta 2.

6. **Updates and Software Management:**
   - Keep your macOS and apps updated for optimal performance and compatibility.
   - Some applications have been updated with native Apple Silicon support, so it's a good idea to check for these versions for better performance.

Remember that Rosetta 2 is a stopgap solution while developers transition their software to native Apple Silicon support. Over time, the need to use Rosetta 2 should diminish as more applications offer native ARM support.

# What about I want to enter a state that all the rest of the command should be executed under x86?

To enter a state where all subsequent commands in a terminal session are executed under the x86 architecture on your M1 Pro Mac, you can start a Rosetta 2 translated shell. This will allow you to run a terminal session entirely under the x86_64 architecture, ensuring that all commands and applications launched from that session are treated as x86.

Here's how you can do it:

1. **Open Terminal.**
2. **Start a Rosetta 2 translated shell by executing the following command:**

   ```bash
   arch -x86_64 /bin/zsh
   ```

   - This command starts a new instance of the Zsh shell (the default shell on macOS) under the x86_64 architecture. If you prefer to use Bash or another shell, you can replace `/bin/zsh` with the path to your preferred shell, like `/bin/bash`.

3. **Verify the Architecture (Optional):**

   - To confirm that you're running under the x86_64 architecture, you can use the `arch` command:
     ```bash
     arch
     ```
   - This should return `i386`, indicating that you are in an x86_64 emulation environment (Rosetta 2 translates x86_64 to i386 for compatibility).

4. **Run Commands:**

   - Now, any command you execute in this shell will automatically run under the x86_64 architecture.

5. **Exit x86 Mode:**
   - When you're done and want to return to the native ARM architecture, simply type `exit` or close the Terminal window. This will end the x86_64 emulation session.

Remember, running commands and applications under Rosetta 2 might have some performance overhead compared to running them natively on your M1 Pro chip. Also, not all x86 applications and commands may work perfectly under Rosetta 2, but most should function as expected.
