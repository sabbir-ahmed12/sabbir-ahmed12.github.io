---
layout: post
title: Building a 3D Slicer CLI Module from Scratch Using ITK and SlicerExecutionModel
date: 2026-03-11 00:16:12-0400
description: A complete tutorial on building a Slicer CLI module using ITK and SlicerExecutionModel
tags: itk slicer cmake c++
categories: tutorial
giscus_comments: true
related_posts: false
---

# Introduction

3D Slicer is a powerful open-source platform widely used for **medical image analysis, visualization, and research**. One of its most powerful features is the ability to extend its functionality using **custom modules**.

In this tutorial we will build a **Slicer Command Line Interface (CLI) module from scratch** using:

- **ITK (Insight Toolkit)** for image processing
- **SlicerExecutionModel** for module integration
- **CMake** for building the project

By the end of this tutorial you will understand:

- How Slicer CLI modules work
- How to configure projects using CMake
- What `GenerateCLP` is and why it is needed
- How to build a module from scratch
- How to load the module into Slicer

---

# Table of Contents

1. Introduction  
2. Understanding CLI Modules in Slicer  
3. Installing Dependencies  
4. Understanding CMake  
5. Building ITK  
6. Building SlicerExecutionModel  
7. Project Structure  
8. Understanding GenerateCLP  
9. Writing the XML Interface  
10. Writing the Thresholding Algorithm  
11. Writing the CMake Configuration  
12. Building the Project  
13. Running the Module  
14. Loading the Module in Slicer  
15. Conclusion  

---

# 1. Introduction

Slicer modules can be implemented in three ways:

| Type | Language | Description |
|-----|------|-------------|
| Scripted modules | Python | Fast development |
| Loadable modules | C++ | High performance |
| CLI modules | C++ executable | Simple integration with automatic GUI |

In this tutorial we focus on **CLI modules**, which are ideal when you want to:

- Integrate an **existing C++ or ITK algorithm**
- Automatically generate a **GUI**
- Allow the algorithm to run **both inside and outside Slicer**

---

# 2. Understanding CLI Modules in Slicer

A Slicer CLI module consists of three components:

```text
XML description
C++ algorithm
CMake build system
```

Workflow:

```text
XML → GenerateCLP → C++ executable → Slicer GUI
```

Steps:

1. XML describes the interface
2. `GenerateCLP` generates command-line parsing code
3. C++ implements the algorithm
4. Slicer loads the executable and automatically creates a GUI

A major advantage of CLI modules is that the **user interface and the algorithm are separated**. You describe the interface once in XML, and SlicerExecutionModel handles the tedious command-line parsing and GUI generation for you.

---

# 3. Installing Dependencies

You will need:

- **CMake**
- **ITK**
- **SlicerExecutionModel**

Clone the repositories:

```bash
git clone https://github.com/InsightSoftwareConsortium/ITK.git
git clone https://github.com/Slicer/SlicerExecutionModel.git
```

If you also want to test the module inside Slicer, make sure you already have a working 3D Slicer installation on your machine.

---

# 4. Understanding CMake

CMake is a **cross-platform build system generator**. Instead of manually writing platform-specific build files, we write one or more `CMakeLists.txt` files that describe:

- Source files
- Libraries
- Dependencies
- Include directories
- Build rules

CMake then generates the actual native build system for your platform.

For example, CMake can generate:

| Generator | Typical Use |
|----------|-------------|
| Unix Makefiles | Standard Linux/macOS terminal builds |
| Ninja | Faster incremental builds |
| Visual Studio | Windows projects |
| Xcode | macOS IDE projects |

## Typical CMake workflow

```text
Source code → CMake configure step → Generated build system → Compiler
```

The modern and clean way to configure a project is:

```bash
cmake -S . -B build
```

Here:

- `-S .` means the source directory is the current directory
- `-B build` means the build files will be generated in the `build` directory

Then build with:

```bash
cmake --build build
```

This is preferred over building directly inside the source tree because it keeps generated files separate from your source code.

## Choosing a generator

You can explicitly choose a generator if needed:

```bash
cmake -S . -B build -G "Unix Makefiles"
```

or

```bash
cmake -S . -B build -G Ninja
```

If you use Unix Makefiles, you may also build with:

```bash
make -C build -j8
```

If you use Ninja, you may build with:

```bash
ninja -C build
```

## Using an interactive CMake interface

If you prefer an interactive terminal interface, you can use:

```bash
ccmake -S . -B build
```

This is helpful when you want to inspect or modify CMake cache variables such as:

- `ITK_DIR`
- `SlicerExecutionModel_DIR`
- build options
- compiler settings

There is also a graphical interface:

```bash
cmake-gui
```

In short, **CMake is necessary because it finds dependencies, configures the project, and generates the correct build files for your platform and compiler**.

---

# 5. Building ITK

Create a build directory:

```bash
mkdir ITK-build
```

Configure:

```bash
cmake -S ./ITK -B ./ITK-build
```

Build:

```bash
cmake --build ./ITK-build
```

If you are using Makefiles, you can also build with multiple CPU cores:

```bash
make -C ./ITK-build -j8
```

On Linux, you can check the number of available CPU cores with:

```bash
nproc
```

After building, remember the path to:

```text
ITK-build
```

You will use this later when configuring your CLI module project.

---

# 6. Building SlicerExecutionModel

Create a build directory:

```bash
mkdir SEM-build
```

Configure:

```bash
cmake -S ./SlicerExecutionModel -B ./SEM-build
```

Build:

```bash
cmake --build ./SEM-build
```

If you are using Makefiles, you can also build with:

```bash
make -C ./SEM-build -j8
```

One important output of this build is the tool:

```text
GenerateCLP
```

This tool is central to how Slicer CLI modules work.

---

# 7. Project Structure

Our project will look like this:

```text
project-root
│
├── CMakeLists.txt
└── src
    ├── CMakeLists.txt
    └── SimpleThreshold
        ├── CMakeLists.txt
        ├── SimpleThreshold.cxx
        └── SimpleThreshold.xml
```

This structure keeps the project organized:

- the root directory manages the whole project
- `src` collects source modules
- `src/SimpleThreshold` contains one CLI module

---

# 8. Understanding GenerateCLP

`GenerateCLP` is a tool provided by **SlicerExecutionModel**.

Its job is to read your module XML file and automatically generate C++ code for command-line parsing. Instead of writing your own parser for arguments such as:

- `--inputVolume`
- `--outputVolume`
- `--lowThreshold`
- `--highThreshold`

you define them once in XML, and `GenerateCLP` creates the corresponding parser code for you.

For example, suppose your XML contains:

```xml
<double>
  <name>lowThreshold</name>
  <longflag>lowThreshold</longflag>
  <default>0</default>
</double>
```

From this, `GenerateCLP` generates support code so that your C++ program can directly use:

```cpp
lowThreshold
```

It also generates a header file named after your module. In this tutorial that file is:

```text
SimpleThresholdCLP.h
```

Inside your `.cxx` file, you include it:

```cpp
#include "SimpleThresholdCLP.h"
```

and then call:

```cpp
PARSE_ARGS;
```

This macro parses the command-line arguments based on the XML description and populates the generated variables automatically.

So `GenerateCLP` is needed because it:

- avoids manual argument parsing
- keeps the XML and the executable interface synchronized
- enables Slicer to generate the GUI automatically from the same interface description

In practice, you usually do **not** run `GenerateCLP` manually. Instead, the CMake macro `SEMMacroBuildCLI(...)` calls it for you during the build.

---

# 9. Writing the XML Interface

The XML file defines:

- Module category
- Visible module title
- Inputs
- Outputs
- Parameters

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>

<executable>

  <category>MyModules</category>
  <title>SimpleThreshold</title>

  <description>
    Simple binary thresholding using ITK.
  </description>

  <parameters>
    <label>Input/Output</label>

    <image>
      <name>inputVolume</name>
      <longflag>inputVolume</longflag>
      <label>Input Volume</label>
      <channel>input</channel>
      <description>Input image volume</description>
    </image>

    <image>
      <name>outputVolume</name>
      <longflag>outputVolume</longflag>
      <label>Output Volume</label>
      <channel>output</channel>
      <description>Output thresholded image volume</description>
    </image>
  </parameters>

  <parameters>
    <label>Threshold Parameters</label>

    <double>
      <name>lowThreshold</name>
      <longflag>lowThreshold</longflag>
      <label>Low Threshold</label>
      <description>Lower threshold value</description>
      <default>0</default>
    </double>

    <double>
      <name>highThreshold</name>
      <longflag>highThreshold</longflag>
      <label>High Threshold</label>
      <description>Upper threshold value</description>
      <default>0</default>
    </double>

    <double>
      <name>insideValue</name>
      <longflag>insideValue</longflag>
      <label>Inside Value</label>
      <description>Value assigned to voxels inside the threshold range</description>
      <default>1</default>
    </double>

    <double>
      <name>outsideValue</name>
      <longflag>outsideValue</longflag>
      <label>Outside Value</label>
      <description>Value assigned to voxels outside the threshold range</description>
      <default>0</default>
    </double>
  </parameters>

</executable>
```

## Brief explanation of the XML

This XML defines the interface of the CLI module.

- `<category>` controls where the module appears in Slicer
- `<title>` controls the displayed module name
- `<parameters>` groups related parameters together in the GUI
- `<image>` defines image inputs and outputs
- `<double>` defines numeric parameters
- `<name>` becomes the generated variable name in C++
- `<longflag>` defines the command-line flag name

Because we use:

```xml
<category>MyModules</category>
```

the module will appear under:

```text
Modules → MyModules → SimpleThreshold
```

This same XML is used both to build the GUI in Slicer and to generate the parser used by the executable.

---

# 10. Writing the Thresholding Algorithm

Now we implement the actual algorithm using ITK.

```text
SimpleThreshold.cxx
```

```cpp
#include "itkImage.h"
#include "itkImageFileReader.h"
#include "itkImageFileWriter.h"
#include "itkBinaryThresholdImageFilter.h"

#include <cstdlib>

#include "SimpleThresholdCLP.h"

int main(int argc, char * argv[])
{
  PARSE_ARGS;

  constexpr unsigned int Dimension = 3;
  using PixelType = short;

  using ImageType = itk::Image<PixelType, Dimension>;
  using ReaderType = itk::ImageFileReader<ImageType>;
  using WriterType = itk::ImageFileWriter<ImageType>;
  using ThresholdFilterType =
      itk::BinaryThresholdImageFilter<ImageType, ImageType>;

  auto reader = ReaderType::New();
  reader->SetFileName(inputVolume);

  auto thresholder = ThresholdFilterType::New();
  thresholder->SetInput(reader->GetOutput());
  thresholder->SetLowerThreshold(static_cast<PixelType>(lowThreshold));
  thresholder->SetUpperThreshold(static_cast<PixelType>(highThreshold));
  thresholder->SetInsideValue(static_cast<PixelType>(insideValue));
  thresholder->SetOutsideValue(static_cast<PixelType>(outsideValue));

  auto writer = WriterType::New();
  writer->SetFileName(outputVolume);
  writer->SetInput(thresholder->GetOutput());

  writer->Update();

  return EXIT_SUCCESS;
}
```

## Brief explanation of the thresholding code

This code performs binary thresholding on a 3D image. It sets every pixel in the range `[lowThreshold, highThreshold]` to `insideValue` and all other pixels to the `outsideValue`.

- `PARSE_ARGS;` loads the parameters defined in the XML
- `Dimension = 3` means we are processing a 3D volume
- `PixelType = short` means the image stores signed-short intensities
- `ImageFileReader` reads the input image
- `BinaryThresholdImageFilter` performs the thresholding
- `SetLowerThreshold(...)` and `SetUpperThreshold(...)` define the valid intensity range
- `SetInsideValue(...)` assigns a value to voxels inside the threshold range
- `SetOutsideValue(...)` assigns a value to voxels outside the threshold range
- `ImageFileWriter` writes the output image

Because ITK chooses the file format from the output filename extension, using:

```text
output.nii.gz
```

will save the result in NIfTI format.

---

# 11. Writing the CMake Configuration

## Root `CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.16)

project(SimpleThresholdModule LANGUAGES CXX)

find_package(ITK REQUIRED)
include(${ITK_USE_FILE})

find_package(SlicerExecutionModel REQUIRED GenerateCLP)
include(${GenerateCLP_USE_FILE})
include(${SlicerExecutionModel_USE_FILE})
include(${SlicerExecutionModel_CMAKE_DIR}/SEMMacroBuildCLI.cmake)

add_subdirectory(src)
```

## Brief explanation

This file configures the overall project.

- `cmake_minimum_required(...)` sets the minimum required CMake version
- `project(...)` names the project and declares the language
- `find_package(ITK REQUIRED)` finds the ITK installation
- `include(${ITK_USE_FILE})` brings in ITK compiler and include settings
- `find_package(SlicerExecutionModel REQUIRED GenerateCLP)` finds SlicerExecutionModel and the `GenerateCLP` tool
- the `include(...)` lines expose the macros needed for building CLI modules
- `add_subdirectory(src)` tells CMake to continue processing the source tree

## `src/CMakeLists.txt`

```cmake
add_subdirectory(SimpleThreshold)
```

## Brief explanation

This file simply tells CMake to process the `SimpleThreshold` module directory.

## Module `CMakeLists.txt`

```cmake
set(MODULE_NAME SimpleThreshold)

SEMMacroBuildCLI(
  NAME ${MODULE_NAME}
  TARGET_LIBRARIES ${ITK_LIBRARIES}
  EXECUTABLE_ONLY
)
```

## Brief explanation

This file builds the actual CLI module.

- `MODULE_NAME` defines the module name
- `SEMMacroBuildCLI(...)` is a helper macro from SlicerExecutionModel
- it runs `GenerateCLP` on `SimpleThreshold.xml`
- it generates `SimpleThresholdCLP.h`
- it builds the executable
- it links the executable against ITK

---

# 12. Building the Project

First configure the project:

```bash
cmake -S . -B build \
  -DITK_DIR=/path/to/ITK-build \
  -DSlicerExecutionModel_DIR=/path/to/SEM-build
```

Then build it:

```bash
cmake --build build
```

If your generator is Unix Makefiles, you can also build with:

```bash
make -C build -j8
```

If you want an interactive configuration interface, you can use:

```bash
ccmake -S . -B build
```

The executable will typically appear in:

```text
build/src/SimpleThreshold/bin
```

---

# 13. Running the Module

Run from the command line:

```bash
SimpleThreshold \
  --inputVolume input.nii.gz \
  --outputVolume output.nii.gz \
  --lowThreshold 100 \
  --highThreshold 500 \
  --insideValue 1 \
  --outsideValue 0
```

This will write the thresholded image to `output.nii.gz`.

You can also inspect the generated command-line interface using:

```bash
./SimpleThreshold --help
```

---

# 14. Loading the Module in Slicer

Start Slicer with:

```bash
Slicer --additional-module-paths /path/to/build/src/SimpleThreshold/bin
```

Your module will appear in:

```text
Modules → MyModules → SimpleThreshold
```

The GUI is generated automatically from the XML file.

If Slicer cannot save additional module paths in its settings, launching it with `--additional-module-paths` is a reliable workaround during development.

---

# 15. Conclusion

In this tutorial we built a **complete Slicer CLI module from scratch** using:

- ITK
- SlicerExecutionModel
- CMake

Key takeaways:

- CLI modules separate **interface, algorithm, and build system**
- XML defines the user interface
- `GenerateCLP` handles argument parsing automatically
- ITK implements the image processing
- CMake manages dependencies and builds the executable
- Useful links:
    - [ITK documentation](https://docs.itk.org/en/latest/)
    - [ITK software guide](https://itk.org/ItkSoftwareGuide.pdf)
    - [Slicer Execution Model](https://www.slicer.org/wiki/Documentation/Nightly/Developers/SlicerExecutionModel)

This workflow makes it much easier to integrate custom medical image processing algorithms into 3D Slicer without manually building a GUI.

---

*Congratulations! You can create a Slicer module using your custom algorithm.*