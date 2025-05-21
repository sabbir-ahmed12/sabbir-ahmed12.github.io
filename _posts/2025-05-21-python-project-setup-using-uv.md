---
layout: post
title: Python Project Setup Using UV
date: 2025-05-21 00:19:00-0400
description: Tutorial Post on Python Project Setup Using UV
tags: python
categories: tutorial
giscus_comments: true
related_posts: false
---

## UV

UV is a new and an extremely fast Python package and project manager, written in Rust.

## Installation

There are different ways to install UV. You can find different ways of installing UV from [here](https://docs.astral.sh/uv/#installation). However, in this tutorial, we'll use `Homebrew` to install UV as it is easier to install packages using `Homebrew` in MacOS and Linux. You can install `Homebrew` from [here](https://brew.sh/). You can install `uv` using `Homebrew` using the following command:

```
brew install uv
```

Once it is installed successfully, you can run the following command to check if that works fine.

```
uv --version
```

It should print the version of `uv`.

## Setup Project Using UV

Let's say you want to create a project named `new_project` in your Desktop. For that, change your current directory to `Desktop` and write the following command:

```
uv init new_project
```

If the command works fine, it'll show a message with the location where it initialized that project. This command will create a new directory in the Desktop called `new_project`. However, if you want to use an existing directory to setup your application, you can run the command below:

```
uv init
```

We aren't using any options in the above commands. However, `uv` has some options to choose from to create project. `--app` is the default option when we run `uv init <project_name>`. That means, it instead runs the command `uv init <project_name> --app` by default. Most of the time we want to use the default option as it creates a simple project structure. We can also use another option `--lib` if we want to work on a project that will be built and distributed as a python package.

The default option will create some files initially in the desired directory. It'll initialize the directory as a `git` repository. So, there will be a `.git` directory in there. The other files are:

- `.gitignore`: contains the files and directories to ignore in the commit.
- `.python-version`: file containing information about the version of python the app is using.
- `main.py`: the main python file of the project.
- `pyproject.toml`: contains information about the project e.g., name, version, description, dependencies etc.
- `README.md`: to add documentation for the project.

## Install Package to the Project

Instead of using `pip`, we'll install required package using the following command in `uv`:

```
uv add <package_name>
```

For example, if you want to install `flask` and `requests` library using `uv`, you can run the following command:

```
uv add flask requests
```

It'll automatically create a virtual environment for the project and show the path of the environment. It'll create a directory called `.venv` in the root directory and update the `pyproject.toml` file to list the dependencies. Moreover, it'll create a new file called `uv.lock` which records the exact version of all the dependencies of the project. It ensures that our environment is perfectly reproducible. You can run the following command from your project directory to visualize and understand the dependencies of your project:

```
uv tree
```
