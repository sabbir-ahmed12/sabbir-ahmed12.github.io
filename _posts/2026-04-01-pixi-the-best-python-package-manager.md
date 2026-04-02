---
layout: post
title: Pixi - Best Package Manager for Python and for Doing Reproducible Research
date: 2026-04-01 00:21:12-0400
description: A hands-on tutorial on setting up a project environment using pixi
tags: python pixi
categories: tutorial
giscus_comments: true
related_posts: false
---

# Pixi
Pixi is a fast, modern, and reproducible package management tool for developers of all backgrounds.

## Introduction

`pip` and `conda` are the default python package manager for Python. However, `uv` has also gained much popularity and became the go-to package manager for many people. The reasons are - it is fast, handles lockfiles, and manages virtual environments out of the box.

However, data science and AI projects rarely stay pure Python. Many key packages such as numpy, psycopg2 etc. contain C, C++ or Fortran extensions that require compiler installed on the system level. `uv` or `pip` can install the Python bindings, but not the system libraries underneath them. So, if you've worked on data science project or if you've used a high performance computing cluster (HPC), you might be familiar with the pip build failures. The reasons you get such errors are - missing system build tools, missing header libraries, missing wheel package etc. In order to compile the C/C++ libraries, windows requires Microsoft C++ build tools and Linux requires `build-essential` and `python3-dev` packages. Moreover, your system might be also missing some system level packages which you need to install using system level package manager such as `apt` or `homebrew`. Since `pip` and `uv` use Python Package Index (PyPI - main package index for Python with a much larger ecosystem than conda) as the source for installing packages, you'll almost always face such errors if you want to setup an environment for your AI or ML project.

`Conda` is a cross-platform, cross-language package ecosystem that allows users to install packages and manage environments. It manages conda packages from the Anaconda repository and Anaconda Cloud. `Conda` packages are binaries so you don't need to have compilers available to install them. `Conda` can create isolated environments with differnt versions of Python whereas `Pip` depends on `virtualenv` or `venv` to create isolated environments. Moreover, `pip` installs dependencies in a recursive, serial loop. No effort is made to ensure that dependencies of all the packages are fulfilled simultaneously which can lead to broken environments. `Conda` takes care of that to help prevent broken environment. However, `conda` can take long time to solve dependencies. Also, it's `environment.yml` file only lists packages that you explicitly install, not all the dependencies underneath. So, recreating the environment later may silently pull different versions of the dependencies and thus the code may not work. Most projects need packages from both `PyPI` and `conda-forge` but neither `pip` nor `conda` checks the other conflicting versions which can break the environment.

`pixi` solves all the problems of `uv`, `pip` and `conda` by managing Python packages from `PyPI` and compiled system libraries from `conda-forge` in a single tool, with automatic lockfiles, fast dependency resolution and stores the environment inside the project itself.

## Installation

Install `pixi` on Linux or macOS using the command below. For windows or other installation method, you can follow the instruction in this [link](https://pixi.prefix.dev/latest/#available-software).

```bash
curl -fsSL https://pixi.sh/install.sh | sh
```

## Initialize a Project

You can create a new directory and initialize a `pixi` project using the `pixi init` command as given below. It creates a `pixi.toml` manifest that tracks dependencies and lives in version control with your code. It automatically detects your platform and sets `conda-forge` as the default channel.

```bash
pixi init <project-name>
cd <project-name>
```
If you run `cat pixi.toml`, you'll find that the `dependencies` section is empty as we haven't installed any package yet. If your project already has a `pyproject.toml` file, `pixi init` will add sections to it automatically. No separate manifest needed.


## Adding Dependencies
You can install packages from both `conda-forge` and `PyPI` as below:

```bash
# conda-forge packages
pixi add python geopandas gdal lightgbm

# PyPI packages
pixi add --pypi scikit-learn
```

The packages and their dependencies will appear in separate sections like `[dependecies]` and `[pypi-dependencies]` for the `conda-forge` and `PyPI` channels respectively.

Each command updates the pixi.toml manifest and regenerates the lockfile automatically. 

## Activating Environment
Now that you've an environment and some packages installed, you need to activate the environment to use the installed packages. You can use the following command to run the file e.g. `train.py` without manually activating the environment:

```bash
pixi run python train.py
```

However, if you want to manually activate it to run a program or for interactive shell session, you can do it by using:

```bash
pixi shell
```

## Multi-Platform Support

If you're working on a MacOS or on a Windows system but later you may need to run the code in a Linux machine, you can add more platforms on your lockfile. `pixi` can generate lockfile entries for every target platform, including system libraries. In the example below, I've added Linux and Windows. If you read the `pixi.toml` file, you can see the target platforms under the `platforms` variable. For more information, read the [documentation](https://pixi.prefix.dev/latest/workspace/multi_platform_configuration/).

```bash
pixi workspace platform add linux-64 win-64
```

This command updates `pixi.toml` file with the new platforms and regenerates the lockfile with entries for all of the platforms.


## Multiple Environments
`pixi` can manage dependencies for separate environments such as - dev or production by not only including Python packages but also system libraries. For example, you can add `pytest` and `ruff` for dev environment using the command below:

```bash
pixi add --feature dev pytest ruff
```

To use the feature, link it to a named environment:

```bash
pixi workspace environment add dev --feature dev
```

These two commands update pixi.toml with the new feature and environment. `dev = ["dev"]` means the dev environment includes default dependencies plus everything under `[feature.dev.dependencies]`.

To use the dev environment, pass `-e dev` to any `pixi` command:

```bash
# Activate an interactive shell
pixi shell -e dev      

# Run a single command
pixi run -e dev pytest  
```

## Built-in Task Runner
`uv` doesn’t have a built-in task runner, so teams typically manage project commands with `Makefiles`, Just, or shell scripts. These commands can be hard to remember. `pixi` has a built-in task runner that stores these commands alongside your dependencies, so no one has to memorize them.

To define a task, use pixi task add:

```bash
pixi task add preprocess "python src/preprocess.py --input data/raw --output data/processed"
pixi task add train "python src/train.py"
pixi task add test "pytest tests/"
```

This adds three tasks to pixi.toml:

```bash
[tasks]
preprocess = "python src/preprocess.py --input data/raw --output data/processed"
train = "python src/train.py"
test = "pytest tests/"
```

To run any task, use pixi run followed by the task name:

```bash
pixi run train
pixi run test
```

Tasks run inside the project environment automatically, with no need to activate first.


## Global Tool Installtion
Both uv (uv tool install) and pixi support installing tools globally. These are tools like code formatters and linters that are useful everywhere but don’t belong in any specific project:

```bash
pixi global install ipython
pixi global install ruff
```

Once installed, they’re available from any directory:

```bash
ipython    # start interactive Python shell
ruff check .  # lint any project
```

## Reproduce the Environment

To reproduce the environment, you just need to run the command below:

```bash
pixi install
```

## Useful Links
- Pixi Homepage: https://pixi.prefix.dev/latest/
- GitHub: https://github.com/prefix-dev/pixi