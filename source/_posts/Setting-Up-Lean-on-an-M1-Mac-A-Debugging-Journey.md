---
title: "Setting Up Lean on an M1 Mac: A Debugging Journey"
date: 2023-10-11 21:08:37
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

Apple's M1 chips have ushered in a new era of computing performance. But with new architectures come new challenges. One such challenge I recently faced was setting up the Lean theorem prover. Here's how I tackled it.

### **The Challenge**

While trying to set up Lean using **`elan`**, I faced an issue where I couldn't download **`lean3`**. It appeared that the links for **`lean3`** were replaced in favor of **`lean4`**, which was causing the 404 errors.

### **Manual Installation**

Since the automated installation wasn't working, I decided to go the manual route. I downloaded the Lean 3.4.2 package for macOS from the [official Lean 3 GitHub releases](https://github.com/leanprover/lean3/releases).

After extracting the ZIP file, I was presented with three folders: **`bin`**, **`include`**, and **`lib`**. This meant that the Lean executable was present and I didn't need to undergo any installation wizards or drag apps into folders.

To use Lean seamlessly, I added the path to Lean's **`bin`** directory to the system's PATH. This would allow me to run Lean from any terminal window without specifying the full path.

### **Elan Integration**

For better version management, I wanted to integrate the manual installation with **`elan`**. After placing the extracted Lean folder under **`~/.elan/toolchains/`**, I tried setting an override with **`elan`**, but faced issues related to a missing library: **`libgmp.10.dylib`**.

### **The GMP Library Issue**

Lean seemed to be expecting a specific version of the GMP library built for the x86_64 architecture, but my system, being an M1 Mac, had the ARM version.

Here's how I resolved it:

1. **Install the x86_64 Homebrew**:

   M1 Macs support both ARM and x86_64 architectures, and Homebrew provides separate installations for both. To ensure I was installing x86_64 software, I had to prefix my commands with **`arch -x86_64`**:

   ```bash
   arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install the x86_64 version of GMP**:

   Using the x86_64 version of Homebrew:

   ```bash
   arch -x86_64 /usr/local/bin/brew install gmp
   ```

   This ensured that Lean found the x86_64 version of the library it was expecting.

3. **Run Lean**:

   After these steps, I was finally able to run:

   ```bash
   ~/.elan/toolchains/lean3-manual/bin/lean --version
   ```

   And voila! Lean was up and running.

### **Conclusion**

Setting up software on newer hardware often requires some tinkering. The M1 transition has introduced challenges, but with a little persistence (and a lot of troubleshooting), it's possible to find a way. Hopefully, as more software gets natively compiled for ARM, these issues will become less frequent.

Remember, whenever you're stuck, the community (and sometimes even a friendly AI assistant) is there to help!
