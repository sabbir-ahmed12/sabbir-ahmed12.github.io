---
layout: post
title: Python Code Formatting and Linting Using Ruff
date: 2026-01-19 00:16:12-0400
description: Tutorial Post on Python Code formatting and Linting
tags: python
categories: tutorial
giscus_comments: true
related_posts: false
---

## Ruff

`ruff` is a super-fast Python linter and formatter written in Rust. It consolidates multiple Python tools like Flake8, Black, isort and others into a single, faster solution that significantly improves code quality. In this tutorial, we'll learn how to use `ruff` from the command line to check and fix our code, and how to configure `ruff` both for individual projects and globally. We'll also learn how to integrate `ruff` with VSCode to get real-time feedback as we code.

## Installation

Assuming we've the `uv` already installed in our system, we can install `ruff` using `uv` globally using the command below:

```bash
uv tool install ruff
```

We can check if `ruff` has been installed properly or not using `ruff --version` command.

## Use Ruff to Check Linting

We can check any issues within a Python file e.g. `main.py` using `ruff` with the following command:

```bash
ruff check main.py
```

However, if we want to check all the files in a directory, we can use:

```bash
ruff check .
```

Ruff can automatically fix some issues. For that, we need to use the command using `--fix` flag. For example:

```bash
ruff check --fix .
```

We can also check what `ruff` will change if we use the `--fix` flag before `ruff` actually changes our code. It's better to be aware of the changes before it breaks the code.

```bash
ruff check --fix --diff .
```

## Use Ruff to Format Code

Ruff comes with `Black` compatible code formatter. We can format our code using `ruff` using the command below:

```bash
ruff format .
```

**Run the `ruff check` command before `run format`**. If we want `ruff` to watch our code for error continuously, we can use the following command:

```bash
ruff check --watch .
```

## Configure Ruff

Ruff comes with some default rules already setup which are non-controversial. However, we can configure `ruff` in our own way. We can configure `ruff` globally to apply the rules to any project and locally to apply rules to a particular project. If we use local configuration, it'll overwrite the global rules.

### Configure Ruff Locally

We can setup `ruff` using either `pyproject.toml` file or `ruff.toml` for individual project. In `pyproject.toml` file, there maybe different sections describing different things related to the project. However, we need a different section to configure `ruff` which is [tool.ruff].

Let's say we want to apply all linting rules in `E` (Pycodestyle) and `F` (Pyflakes) but we want to ignore a particular linting rule such as `F401`, we can do it in the following way:

```toml
[tool.ruff.lint]
select = ["E", "F"]
ignore = ["F401"]
```

If we instead want to use `ruff.toml` file, we can write it as below:

```toml
[lint]
select = ["E", "F"]
ignore = ["F401"]
```

You can learn more about the linting and formatting rules from the link given at the end of the post.

### Configure Ruff Globally

To use global configuration for `ruff`, we can create a directory at `~/.config` called `ruff` and create a file named `ruff.toml` in that directory. For Windows, it can be inside the `AppData\Roaming` directory. Once the rules are set in the `ruff.toml` file, we can use the same `ruff check` command to look for issues in the code using the global configuration. We can write `ruff.toml` file as given in the [here](https://docs.astral.sh/ruff/configuration/).

### Integrate Ruff into VSCode

We can also integrate `ruff` in the VSCode to fix the linting and formatting issues as we code. For that, go to the `Extensions` option and search for `Ruff`. Install the one from `Atral Software`. Once installed, you can update your `settings.json` file to use `ruff`. You can get help if you read the details of extension documentation but you can do it in the following way:

- Press `command + shift + p` to open the command palette.
- Type `Preferences: Open User Setting (JSON)`.
- Press `Enter` to open the `setting.json` file.
- Check if you've any rules already setup for `Python`. Otherwise, you can paste the following:

```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    },
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

However, we can still use the `pyproject.toml` and `ruff.toml` files in our project to overwrite the rules of the extension.

## Useful Links

Ruff documentation - https://docs.astral.sh/ruff/

Ruff rules - https://docs.astral.sh/ruff/rules/

Video tutorial - https://youtu.be/828S-DMQog8
