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
---

`uv` is a new and an extremely fast `Python` package and project manager, written in Rust. `uv` uses a global caching system which makes it more efficient in terms of storage and package installation time. Unlike `pip` and other package managers, if there are multiple projects that needs a package (for example - `flask`) at a time, `uv` will install it once and use the same package for all the current projects without any issue which saves storage space by avoiding duplicate installation and also saves time.


## Installation
---

There are different ways to install UV. You can find different ways of installing UV from [here](https://docs.astral.sh/uv/#installation). However, in this tutorial, we'll use `Homebrew` to install UV as it is easier to install packages using `Homebrew` in MacOS and Linux. You can install `Homebrew` from [here](https://brew.sh/). You can install `uv` using `Homebrew` using the following command:

```bash
brew install uv
```

Once it is installed successfully, you can run the following command to check if that works fine.

```bash
uv --version
```

It should print the version of `uv`.


## Setup Project Using UV
---

Let's say you want to create a project named `new_project` in your Desktop. For that, change your current directory to `Desktop` and write the following command:

```bash
uv init new_project
```

If the command works fine, it'll show a message with the location where it initialized that project. This command will create a new directory in the Desktop called `new_project`. However, if you want to use an existing directory to setup your application, you can run the command below:

```bash
uv init
```

We aren't using any options in the above commands. However, `uv` has some options to choose from to create project. `--app` is the default option when we run `uv init <project_name>`. That means, it instead runs the command `uv init <project_name> --app` by default. Most of the time we want to use the default option as it creates a simple project structure. We can also use another option `--lib` if we want to work on a project that will be built and distributed as a python package.

The default option will create some files initially in the desired directory. It'll initialize the directory as a `git` repository. So, there will be a `.git` directory in there. The other files are:

- `.gitignore`: contains the files and directories to ignore in the commit.
- `.python-version`: file containing information about the version of python the app is using.
- `main.py`: the main python file of the project.
- `pyproject.toml`: contains information about the project e.g., name, version, description, dependencies etc.
- `README.md`: to add documentation for the project.


## Install and Remove Python Package
---

Instead of using `pip`, we'll install required package using the following command in `uv`:

```bash
uv add <package_name>
```

For example, if you want to install `flask` and `requests` library using `uv`, you can run the following command:

```bash
uv add flask requests
```

It'll automatically create a virtual environment for the project and show the path of the environment. It'll create a directory called `.venv` in the root directory and update the `pyproject.toml` file to list the dependencies. Moreover, it'll create a new file called `uv.lock` which records the exact version of all the dependencies of the project. It ensures that our environment is perfectly reproducible. You can run the following command from your project directory to visualize and understand the dependencies of your project:

```bash
uv tree
```

Now, to remove a `python` package from your environment, use the following command:

```bash
uv remove <package_name>
```

`uv` will automatically update the `uv.lock` and `pyproject.toml` file when you add and remove a package.

## Run Python Script in UV Environment
---

Now we have a `python` environment setup for our project. In old system, to run a `python` script, we used to use the command `python <script.py>`. However, as you've noticed that we don't activate any environment in `uv`. Therefore, in order to run a script in `uv` we use the command:

```bash
uv run <script_name.py>
```

For example - to run `main.py` file in your project, you'll write `uv run main.py`. Now, to make sure that `uv` is using the python environment created for that particular project, you can write the following code in your `main.py` file and then run the `uv run main.py` command:

```python
import sys


def main():
    print(sys.executable)


if __name__ == "__main__":
    main()
```

If you run your `main.py` file now, you'll see that the python executable path is `<path>/<your_app_name>/.venv/bin/python3` which means that `uv` is using the correct `python`. 

**NOTE:** ***The good thing about `uv` is that even if you delete the `.venv/` directory from the project directory and then you run the `uv run main.py` command, `uv` will automatically create the environment and install the required packages using the `uv.lock` file and run the command.***


## Replicate UV Python Environment
---

Let's say you want to share your code with others. Whoever wants to run your code, that person needs to create the same environment in order to run the code successfully in the new machine. However, you don't ship your virtual environment with your code because it'll increase the size of the repository.

`uv` can create the exact environment using the `uv.lock` and `pyproject.toml` file shipped along with the code. To do that, use the following command:

```bash
uv sync
```


## Migrate from `pip` to `uv`
---

If you've an existing project created either using `pip` or other package managers with a `requirements.txt` file and you want to migrate that project to `uv` environment, you can migrate using the following commands:

1. Create the `uv` environment project structure.

```bash
uv init
```

2. Install the packages

```bash
uv add -r requirements.txt
```

Now, if you want to rely solely on `uv` in future, you can delete the `requirements.txt` file.


## Replace `pipx` with `uv`
---

`pipx` is used to install tools such as formatter, linter etc in to format and write python code following best practices since we don't want to install those tools for a particular project separately. So, `pipx` installs those tools globally which we can now do using `uv`.

For example - if you want to install `ruff` using `uv`, you run the following command:

```bash
uv tool install ruff
```
This command installs ruff and will add to the `PATH`. You can check the path using `which ruff` command.

If you want to uninstall a tool such as `ruff` installed using `uv`, you can run the command below:

```bash
uv tool uninstall ruff
```
You can list the tools installed in your machine using `uv` using the command:

```bash
uv tool list
```
You can also upgrade all of your tools at once using the following command:

```bash
uv tool upgrade --all
```

`uv` can also use tools like `ruff` without installing it permanently on your device. If you want to use tools for some of your project only a few times, `uv` will make your life easier. You can run the following command to run `ruff check` command without installing `ruff`:

```bash
uv tool run ruff check
```
Alternatively, you can use the short command:

```bash
uvx ruff check
```

That's all for this blog. Although `uv` has a lot of functionalities which weren't covered in this blog, I'll try to add them as I become more familiar with the other functionalities.