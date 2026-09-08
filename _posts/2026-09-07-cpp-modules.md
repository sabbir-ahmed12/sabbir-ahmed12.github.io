---
layout: post
title: Modern C++ - Using Modules
date: 2026-09-07 01:14:00-0400
description: Learn about using modern C++ module
tags: C++
categories: tutorial
giscus_comments: true
related_posts: true
---


# Overcoming Apple Clang: A Guide to C++20 Modules on macOS

If you are diving into modern C++ software engineering, you have likely encountered the promise of **C++ Modules**. Introduced in C++20 and refined in C++23, modules aim to replace the decades-old `#include` preprocessor mechanism. They offer significant improvements in build times, isolation from macro pollution, and a cleaner way to structure high-performance C++ applications.

However, if you develop on a Mac, you might hit a brick wall when trying to build a module-based project with standard tools like CMake. 

This guide breaks down exactly why macOS struggles with C++ modules out of the box, how to set up the correct toolchain, and how to build a standard module-based project using CMake and Ninja.

---

## The Problem: Apple Clang vs. Upstream LLVM

If you install Xcode or the macOS Command Line Tools, your default compiler is **Apple Clang**. While Apple Clang is fantastic for building iOS apps with Swift and Objective-C, it lags significantly behind the global C++ standard.

When CMake (version 3.28+) configures a C++ module project, it doesn't just compile files blindly. It must scan your source code to map out the dependency graph (which module imports which) to ensure they are compiled in the exact right order. 

To do this, CMake relies on a background tool called `clang-scan-deps`. 

**The catch:** Apple strips `clang-scan-deps` out of their Apple Clang distribution because their internal Xcode build system doesn't rely on it. As a result, when you run CMake on a Mac, you get a fatal error complaining that the compiler cannot discover the import graph dependencies.

## The Solution: Installing the Upstream Toolchain

To fix this, we need to bypass Apple Clang entirely and use the industry-standard upstream **LLVM** toolchain, alongside the **Ninja** build system (which is highly optimized for complex module dependencies).

### 1. Install the Tools

We will use Homebrew to install the official LLVM package and Ninja:

```bash
brew install llvm ninja
```

### 2. Configure Your Environment

Homebrew installs LLVM as "keg-only" so it doesn't break macOS system tools. To make your terminal use this modern toolchain by default, add the following to your `~/.zshrc` (or `~/.bash_profile`):

```bash
# Prioritize Homebrew LLVM over Apple Clang
export PATH="$(brew --prefix llvm)/bin:$PATH"

# Set global compiler variables for CMake
export CC="$(brew --prefix llvm)/bin/clang"
export CXX="$(brew --prefix llvm)/bin/clang++"

# Linker and Include flags
export LDFLAGS="-L$(brew --prefix llvm)/lib"
export CPPFLAGS="-I$(brew --prefix llvm)/include"
```

Reload your shell configuration:
```bash
source ~/.zshrc
```

Verify you are now using the correct compiler (it should say "Homebrew clang version...", not "AppleClang"):
```bash
clang++ --version
```

---

## A Standard Project Walkthrough

Let's build a minimal, standard C++ project utilizing modules, CMake, and our new LLVM toolchain. 

### Project Structure

```text
MathApp/
├── CMakeLists.txt
├── MathModule.cppm    # The module interface
└── main.cpp           # The main executable importing the module
```

*(Note: We use `.cppm` for the module interface to clearly distinguish it from standard `.cpp` source files). However, it is not a mandatory requirement but a community convention favored within the Clang and GCC open-source community. You can absolutely use `.cpp` or `.cxx` extensions for modules as long as the file contains an `export module` declaration. `.ixx` is the default and most common extension used by Microsoft's MSVC toolchain.*

### 1. The Module Interface (`MathModule.cppm`)

This file defines our module and exports a simple math function.

```cpp
export module MathModule;

export namespace math {
    int square(int a) { 
        return a * a; 
    }
}
```

### 2. The Main Application (`main.cpp`)

Notice how we use `import` instead of `#include` for our custom module.

```cpp
#include <iostream>

import MathModule;

int main() {
    int value = 4;
    std::cout << "The square of " << value << " is: " 
              << math::square(value) << std::endl;
    return 0;
}
```

### 3. The Build System (`CMakeLists.txt`)

CMake 3.28 introduced native support for `FILE_SET CXX_MODULES`, which handles all the complex precompilation under the hood.

```cmake
cmake_minimum_required(VERSION 3.28)
project(MathAppProject CXX)

# Enforce modern C++ standards
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define the executable
add_executable(MathApp main.cpp)

# Register the module interfaces for dependency scanning
target_sources(MathApp
  PRIVATE
    FILE_SET CXX_MODULES FILES 
      MathModule.cppm
)
```

---

## Building and Running the Application

With our files in place and our terminal environment pointing to the Homebrew LLVM compiler, we can now configure and build the project flawlessly.

**Step 1: Configure with Ninja**
Run this in the root of your `MathApp` directory. The Ninja generator is crucial here, as it perfectly handles the dynamic dependency scanning required by C++ modules.

```bash
cmake -G Ninja -S . -B build
```

**Step 2: Build the Executable**
CMake will utilize `clang-scan-deps` (now available via LLVM) to build the `.pcm` (Precompiled Module) files first, followed by the main executable.

```bash
cmake --build build
```

**Step 3: Run the App**
```bash
./build/MathApp
```

**Expected Output:**
```text
The square of 4 is: 16
```

## Conclusion

Transitioning to C++ modules on macOS requires a slight detour from Apple's default ecosystem. By equipping your workstation with the upstream LLVM toolchain and Ninja, you unlock the full capability of modern C++23 features, robust dependency scanning, and scalable build infrastructure. 
