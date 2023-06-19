---
layout: post
title: How to Install App in Linux?
date: 2023-06-19 08:00:00-0000
description: Tutorial on installing and removing package in linux.
tags: linux
categories: tutorial
giscus_comments: true
related_posts: true
---

Unlike windows, linux and unix operating systems (OS) uses package managers to install app. Development platforms like `Node.js`, `Python` also use package managers (e.g., `npm`, `pip`) to install packages. A package manager keeps track of what package is installed and helps automate the process of installing, upgrading, configuring, and removing packages in a consistent mannner. Linux uses a package manager called `apt` in short form. It stands for `advanced packaging tool`. `apt` is the newer package manager for linux. We can also use `apt-get` which is mostly seen in online tutorials. We'll use `apt` as it is easier to use.

Let's say we want to install a package called `nano`. Nano is an editor mostly used in linux for editing text or code. If `nano` is already installed in your system, it will open when you type `nano` in your terminal and hit enter. However, if `nano` is not available in your system, an error `nano: command not found` will appear.

Now if you type `apt install nano` in your terminal and hit enter, you're most likely to get an error such as `Unable to locate package nano`. This is because linux did not find that package in the package index list. Apt packages index is a database that stores the list of software packages from the repository. You can check the list of packages by running the command `apt list` in your terminal.

To get the updated list of packages, either run `apt update` or `sudo apt update` to run under the sudo privileges. It will collect the latest packages information from all configured sources defined in the `/etc/apt/sources.list` file and the `/etc/apt/sources.list.d` directory and download the information about all packages for your repository and PPA's from the internet. The `apt update` command only updates the list of packages, and no package is upgraded by it.

We have an updated list of packages. You can always check whether the list contains `nano` package using the `apt list` command. In order to install the `nano` package, you need to run the `sudo apt install nano` command. It will download and install the nano editor. After that you can verify that by typing `nano` in the terminal and pressing enter to open the nano editor.

Finally, to remove nano, you need to use the command `sudo apt remove nano`. It will remove the package along with its dependencies. To check the list of available `apt` soft commands, you can run the `apt` command in your terminal.

That's it. You should now be comfortable with the `apt` package manager. Best of luck!
