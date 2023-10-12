---
title: Installing LEAN on arm based Mac — Manually compile and install
date: 2023-10-12 12:00:13
tags:
  [
    Learning Log,
    LEAN,
    automated theorem proving,
    Homebrew,
    Programming Tips,
    arm based system,
  ]
categories:
  - Daily Logs
  - Debug Logs
---

# Installing LEAN on arm based Mac — Manually compile and install

This is a tutorial on how to compile LEAN from source code and install it. Next, we will see how to move it to `elan` (next post)

# Problem with `elan`

`elan` does not provide any installation for lean3 on arm based Mac anymore.

```bash
❯ elan install lean3
info: downloading component 'lean'
Error(Download(HttpStatus(404)), State { next_error: None, backtrace: InternalBacktrace { backtrace: None } })
error: could not download nonexistent lean version `lean3`
info: caused by: could not download file from 'https://github.com/leanprover/lean4/releases/expanded_assets/lean3' to '/Users/ray/.elan/tmp/i3pouyka8f1tcrdn_file'
info: caused by: http request returned an unsuccessful status code: 404
```

Thus, we should install LEAN manually, there are two choices:

1. Download the binary file directly, then put to `~/.elan/toolchains/`, I demonstrated how to do that in my blog: **[Setting Up Lean on an M1 Mac: A Debugging Journey](https://blog.slray.com/2023/10/11/Setting-Up-Lean-on-an-M1-Mac-A-Debugging-Journey/)**
2. Download the source code, compile yourself, install, then move the binary files to `elan` folder.

**This blog will focus mainly on the second way.**

## Links for downloading LEAN:

- Community: This could be confusing. You might checked the link at https://github.com/leanprover-community/mathlib, yet this will lead you to the [official website](https://leanprover-community.github.io/lean3/get_started.html). If you want to install it manually (so that you can regulate versions), you want to check **[this link](https://github.com/leanprover-community/lean)**, which is written in the [OLD_README.md](https://github.com/leanprover-community/mathlib/blob/master/OLD_README.md).
- Official release: https://github.com/leanprover/lean3/releases
  - For arm based mac, you may want to download the `lean-3.4.2-darwin.zip`, but you might meet the problem of the following
  ```bash
  ❯ ./lean --version
  dyld[22512]: Library not loaded: /usr/local/opt/gmp/lib/libgmp.10.dylib
    Referenced from: <A8A24233-8885-3A1A-83F9-0071B27AF08A> /Users/ray/.elan/toolchains/lean3-manual/bin/lean
    Reason: tried: '/usr/local/opt/gmp/lib/libgmp.10.dylib' (no such file), '/System/Volumes/Preboot/Cryptexes/OS/usr/local/opt/gmp/lib/libgmp.10.dylib' (no such file), '/usr/local/opt/gmp/lib/libgmp.10.dylib' (no such file), '/usr/local/lib/libgmp.10.dylib' (no such file), '/usr/lib/libgmp.10.dylib' (no such file, not in dyld cache)
  [1]    22512 abort      ./lean --version
  ```

# Download source code for LEAN (community)

The process is similar for official release. You can download either `Source code(zip)` or `Source code(tar.gz)` from the official release.

## Download source code for specific version

Remember, clone this link: https://github.com/leanprover-community/lean

Here are the full steps (I will use v3.42.1 as an example):

```bash
git clone https://github.com/leanprover-community/lean
cd lean
git checkout v3.42.1
```

Here is the full output (don’t copy this)

```bash
❯ git clone https://github.com/leanprover-community/lean
Cloning into 'lean'...
remote: Enumerating objects: 152824, done.
remote: Counting objects: 100% (452/452), done.
remote: Compressing objects: 100% (220/220), done.
remote: Total 152824 (delta 242), reused 324 (delta 205), pack-reused 152372
Receiving objects: 100% (152824/152824), 55.76 MiB | 14.88 MiB/s, done.
Resolving deltas: 100% (112549/112549), done.
❯ cd lean
❯ ls
LICENSE   bors.toml images    script    tmp
README.md doc       leanpkg   src
bin       extras    library   tests
❯ git checkout v3.42.1
Note: switching to 'v3.42.1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 68455b087 chore(*): release 3.42.1c (#707)
```

# Compile and Install

To compile Lean from this source code, you typically use tools like **`cmake`** and **`make`**.

Here's a step-by-step guide to building Lean 3.4.2 from source on macOS:

1.  **Install Required Dependencies**:
    First, ensure you have the necessary dependencies installed. You'll typically need: - **`cmake`**: for configuring the build process. - **`g++`** or an equivalent C++ compiler: for compiling the source code.
    If you use Homebrew, you can install them using:

    ```bash
    brew install cmake gcc
    ```

2.  **Navigate to the Source Directory**:
    Change directory to the root of the source code:
    If you followed the steps aforementioned, you don’t need to do this

    ```bash
    cd path/to/your/lean
    ```

3.  **Configure and Build**:
    Now, configure and compile Lean:

    ```bash
    mkdir -p build/release
    cd build/release
    cmake ../../src
    make
    ```

    This will create a **`release`** build of Lean. The binary will be available in the **`build/release`** directory once the build process completes.
    This step take a while and you might see some warning. Those are fine as long as no error. Here is an example of warning

    ```bash
    ...
    .../lean/src/library/typed_expr.cpp:59:18: warning: 'write' overrides a member function but is not marked 'override' [-Winconsistent-missing-override]
        virtual void write(serializer & s) const {

    .../lean/src/kernel/expr.h:370:18: note: overridden virtual function is here
        virtual void write(serializer & s) const = 0;
                        ^
    4 warnings generated.
    ```

4.  **Installation (Optional)**:
    If you wish to install Lean system-wide (or in a specified directory), you can use the **`install`** target with **`make`**:

    ```
    make install
    ```

    By default, this might install Lean to **`/usr/local/bin`**. If you wish to specify a different install directory, you can provide a prefix during the **`cmake`** configuration step:

    ```ruby
    cmake -DCMAKE_INSTALL_PREFIX:PATH=/path/to/your/desired/install/location ../../src
    ```

5.  **Using with `elan`**:
    If you wish to integrate this build with **`elan`**, you can either: - Copy the binaries to the appropriate **`elan`** toolchain directory. - Or, point your system's **`PATH`** variable to the directory containing the compiled binaries.
6.  **Potential Issues**:
    - Keep an eye out for any error messages during the build process. They can indicate missing dependencies or other issues.
    - Ensure you have adequate disk space and permissions to write to the specified directories.

Once you've successfully built Lean from source, you should be able to run the **`lean`** binary and verify its version using **`lean --version`**.

Now, you have successfully installed LEAN, try it out by

```bash
cd ..
cd ..
bin/lean --version
```

It should output something like

```bash
Lean (version 3.42.1, commit 68455b087d87, Release)
```

Now you have installed LEAN, in my next blog, I will demonstrate how to put your manually installed LEAN to `elan`
