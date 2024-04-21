---
title: Import LEAN into elan
date: 2023-10-12 22:47:59
tags:
  [
    Learning Log,
    LEAN,
    elan,
    automated theorem proving,
    Programming Tips,
    arm based system,
  ]
categories:
  - Daily Logs
  - Debug Logs
---

# Import LEAN into `elan`

Notice:

- I will use lean v3.42.1 for the demonstration

This is a tutorial on how to override/import LEAN into `elan`, so that you can regulate different versions of LEAN with a version control system.

# Problem with `elan`,

As I mentioned in the blog: [https://blog.slray.com/2023/10/12/Installing-LEAN-on-arm-based-Mac-—-Manually-compile-and-install/](https://blog.slray.com/2023/10/12/Installing-LEAN-on-arm-based-Mac-%E2%80%94-Manually-compile-and-install/)

The problem with `elan` is that when you are trying to download LEAN3, it pops:

```bash
❯ elan install lean3
info: downloading component 'lean'
Error(Download(HttpStatus(404)), State { next_error: None, backtrace: InternalBacktrace { backtrace: None } })
error: could not download nonexistent lean version `lean3`
info: caused by: could not download file from 'https://github.com/leanprover/lean4/releases/expanded_assets/lean3' to '/Users/ray/.elan/tmp/i3pouyka8f1tcrdn_file'
info: caused by: http request returned an unsuccessful status code: 404
```

Here are the instructions on how to install LEAN manually (download source code, and compile):

- [Installing LEAN on arm based Mac — Manually compile and install](https://blog.slray.com/2023/10/12/Installing-LEAN-on-arm-based-Mac-%E2%80%94-Manually-compile-and-install/)
- [Setting Up Lean on an M1 Mac: A Debugging Journey](https://blog.slray.com/2023/10/11/Setting-Up-Lean-on-an-M1-Mac-A-Debugging-Journey/)

# Check your LEAN

Now you have install LEAN, if you `make install` ed, you can use

```bash
lean --version
```

globally. If not, you can still check by

```bash
path/to/your/lean --version
```

If you do not know the path, you may use the following command

```bash
where lean
```

This will generate a path like

```bash
❯ where lean
/Users/ray/.elan/bin/lean
/usr/local/bin/lean
/usr/local/bin/lean
```

Since you have not imported the lean into your `elan`, you might not see the first line.

# Import LEAN to `elan`

Now, to integrate it with `elan`, follow these steps:

1. **Locate the Binaries**: (mentioned above)
   If you've installed Lean using `make install`, by default, the binaries are likely placed in `/usr/local/bin`. However, if you specified a custom installation path using the `DCMAKE_INSTALL_PREFIX` option, then the binaries will be in the `bin` subdirectory of that location.

For example, if you set the install prefix to `/path/to/your/desired/install/location`, then the binaries should be in `/path/to/your/desired/install/location/bin`.

1.  **Integrate with `elan`**:

    - First, find out where `elan` stores its toolchains on your system. This is typically in the `~/.elan/toolchains` directory.
    - Create a new directory inside `toolchains` with a recognizable name, e.g., `lean3.42.1-cmake`.
    - Copy the binaries (from the location in step 1) into this new directory.

    For example:

    ```bash
    mkdir ~/.elan/toolchains/lean3.42.1-cmake
    cp /path/to/your/binaries/* ~/.elan/toolchains/lean3.42.1-cmake/

    ```

2.  **Set the Custom Build as Default**:
    Use `elan` to set this custom build as the default version:
    `bash
elan default lean3.42.1-cmake
`
3.  **Verify the Installation**:
    Now, when you run `lean --version`, it should use your custom-built version. Verify this to ensure everything is set up correctly.
4.  **Important Note**: When integrating custom builds with `elan`, be cautious about updating `elan` or changing configurations. Custom toolchains might be overwritten or disrupted. Always have a backup of your custom build to ensure you can restore it if needed.

This process should allow you to use your custom-built version of Lean with `elan`.

# `make install` detailed example

The process is very similar.

1. **Prepare a New Toolchain Directory for `elan`**:

   First, create a directory inside `elan`'s toolchains folder. This is where you'll place the custom-built version of Lean.

   ```bash
   mkdir ~/.elan/toolchains/lean3.42.1-cmake

   ```

2. **Copy the Lean Binaries**:

   Copy the `lean`, `leanchecker`, and `leanpkg` binaries (and any other related binaries) from `/usr/local/bin` to the custom toolchain directory you just created.

   ```bash
   cp /usr/local/bin/lean ~/.elan/toolchains/lean3.42.1-cmake/bin/
   cp /usr/local/bin/leanchecker ~/.elan/toolchains/lean3.42.1-cmake/bin/
   cp /usr/local/bin/leanpkg ~/.elan/toolchains/lean3.42.1-cmake/bin/

   ```

3. **Set the Custom Build as Default in `elan`**:

   Use `elan` to set this custom build as the default version:

   ```bash
   elan default lean3.42.1-cmake
   ```

4. **Verification**:

   After setting the custom build as default, you can check which version of Lean is being used by running:

   ```bash
   lean --version
   ```

   This should point to your custom-built version. Also, `elan show` will tell you the currently active toolchain.

Remember, by integrating with `elan`, you gain the advantage of easily switching between different versions of Lean. However, when you update `elan` or modify its settings, be cautious. Custom toolchains might be affected, so it's always good to keep a backup of your custom build.

# Problem of `.../eanpkg/main.lean' not found`

I downloaded the correct version of lean, and installed it, and with elan set it as default and global. But I still got

```bash
❯ leanpkg configure
<unknown>:1:1: error: file '..../.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpkg/main.lean' not found
```

Notice that the command `leanpkg configure` is not related to this blog, this blog is one of the series of installing miniF2F Github repo(which is also why I have to install lean3 instead of 4). You might still, however, encounter the same problem if your project is using the same command.

---

## Solution

The error suggests that `leanpkg` is looking for its main Lean file (i.e., `main.lean`) in the custom toolchain directory but isn't finding it. This is likely because when we set up the custom toolchain, we primarily focused on the binaries and didn't account for the other necessary files and directories that `leanpkg` and Lean itself might rely on.

To resolve this:

1. **Copy Necessary Files**:

   In addition to the binaries, you may need to copy over other required files or directories from the compiled Lean source to the custom toolchain directory. In this case, it's specifically looking for the `leanpkg` directory and its contents.

   From the root of your Lean source directory (where you compiled Lean), you can do:

   ```bash
   cp -r path/to/lean3.42.1-cmake-source/leanpkg ~/.elan/toolchains/lean3.42.1-cmake/
   ```

   This assumes the source for `3.42.1` is in `path/to/lean3.42.1-cmake-source/` and that you named your custom toolchain `lean3.42.1-cmake`. Adjust the paths accordingly.

2. **Re-run `leanpkg`**:

   After copying the necessary files, try running `leanpkg configure` again.

3. **Check for Further Missing Files**:

   If you encounter more errors about missing files, you'll need to repeat the process: identify the missing files or directories, copy them from the Lean source directory to the custom toolchain directory, and then retry the command.

Keep in mind that manually setting up a custom toolchain can be a bit tricky since we're trying to replicate the exact structure and contents that Lean expects. It may require some trial and error to get everything right.

If you continue to face issues, another option might be to bypass `elan` temporarily for `leanpkg` operations and use the binaries directly, but integrating with `elan` is more convenient in the long run.

# “Not a directory” problem

I encountered the problem of

```bash
❯ cp -r leanpkg ~/.elan/toolchains/lean3.42.1-cmake/
cp: /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg: Not a directory
cp: leanpkg: unable to copy extended attributes to /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg: Operation not permitted
cp: /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpkg.path: Not a directory
cp: /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpkg: Not a directory
cp: leanpkg/leanpkg: unable to copy extended attributes to /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpkg: Not a directory
cp: /Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpkg/git.olean: Not a directory
```

while I was moving the files. The problem mainly suggest:

1. Permission not enough
2. There are files conflict

## solution

1. **Remove any conflicting files or directories**:

   It seems there might already be a file named `leanpkg` in your custom toolchain directory. We need to ensure that there are no conflicts before copying over the directory.

   ```bash
   rm -rf ~/.elan/toolchains/lean3.42.1-cmake/leanpkg
   ```

   This command will remove the `leanpkg` file or directory in your custom toolchain directory if it exists.

2. **Copy the `leanpkg` directory again**:

   From your Lean source directory:

   ```bash
   cp -r leanpkg ~/.elan/toolchains/lean3.42.1-cmake/
   ```

This should copy the `leanpkg` directory without any conflicts. If you encounter more permission errors, you might need to use `sudo` with the `cp` command, but only do so if you're sure about the paths and operations, as using `sudo` can affect system-critical files if used incorrectly.

# `leanpkg` is looking for wrong LEAN

I installed different versions of LEAN, I previously installed one for 3.4.2 (official release). While installing, I met problem of that `leanpkg` is looking for the file under `'/Users/ray/.elan/toolchains/lean3.4.2-cmake/leanpkg/leanpk` instead of `'/Users/ray/.elan/toolchains/lean3.42.1-cmake/leanpkg/leanpk`

## Solution

The discrepancy you're seeing suggests that somewhere, `leanpkg` is still referencing the old toolchain path (from `3.4.2-cmake`) instead of the new one (`3.42.1-cmake`). This can happen due to a few reasons:

1. **Elan Configuration**: If you've set the default version in `elan` to `lean3.4.2-cmake` or there's an override active, then tools will default to that version.

   To ensure you're using `3.42.1-cmake` as the default:

   ```bash
   elan default lean3.42.1-cmake
   ```

2. **Local Project Configuration**: If you're in a Lean project directory, there might be a `.lean-version` file or a `lean_version` field in the `leanpkg.toml` file that specifies which version to use. Make sure this is set to `3.42.1` or delete the `.lean-version` file to default to the global setting.
3. **PATH Issues**: It's also possible that there's some issue with how your `PATH` environment variable is set up. Maybe the older version's path is taking precedence. You can check which `leanpkg` is being used by:

   ```bash
   which leanpkg
   ```

   This will tell you the path of the `leanpkg` binary that's currently being executed.

4. **Binary References**: The `leanpkg` binary itself might be hardcoded or configured to reference paths from its compilation or installation. This is less likely, but it's possible.

For immediate relief:

1. **Rename Old Toolchain**: Temporarily, you can rename the old toolchain directory to something else to ensure it's not being used:

   ```bash
   mv ~/.elan/toolchains/lean3.4.2-cmake ~/.elan/toolchains/lean3.4.2-cmake-backup
   ```

   This way, even if something is trying to reference the old toolchain, it won't find it, forcing it to default to the available toolchain.

By systematically following these checks and changes, you should be able to pinpoint and resolve the issue. If all else fails, you may need to perform a fresh setup of `elan` and your Lean installations to clear out any lingering configurations.
