---
layout: post
title: Learning Linux from Basics to Advanced
date: 2024-07-14 11:03:00-0400
description: Tutorial post on linux operating system.
tags: linux
categories: tutorial
giscus_comments: true
related_posts: false
---

Learning Linux is one of the most valuable skills in the tech industry. It helps doing things faster and more efficiently. Many of the world's powerful servers and supercomputers run on Linux.

In this post, we'll learn the basics of Linux command line, and then transition to more advanced topics like shell scripting and system administration.

**Important Note:** All examples of this post are demonstrated in Ubuntu 22.04 LTS. This blog was written assuming that you've a Linux OS already setup for you.

## Part 1: Introduction to Linux

### 1.1. Getting Started with Linux

#### What is Linux?

Linux is an open-source operating system that is based on the Unix Operating System (OS). It was created by Linus Torvalds in 1991. Open source means that the source code of the operating system is available to the public. This allows you to modify the original code, customize it, and distribute the new operating system to potential users.

#### Why should you learn linux?

In today's data center landscape, Linux and Microsoft Windows stand out as the primary contenders, with Linux having a major share.

Some of the compelling reasons to learn Linux are:

- Given the prevalence of Linux hosting, there is a high chance that your application will be hosted on Linux. So, learning Linux as a developer becomes increasingly valuable.

- With cloud computing becoming the norm, chances are high that your cloud instance will rely on Linux.

- Linux serves as the foundation for many operating systems for the Internet of Things (IoT) and mobile applications.

- Most of the commands you'll learn can also used in MacOS. Moreover, knowledge of Linux will open many opportunities for you.

#### What is a Linux Kernel?

The kernel is the central component of an operating system that manages the computer and its hardware operations. It handles memory operations and CPU time. It acts as a bridge between applications and the hardware-level data processing using inter-process communication and system calls.

The kernel is loaded into memory first when an operating system starts and remains there until the system shuts down. It is responsible for tasks like disk management, task management, and memory management.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/posts_img/2024_07_14_learning_linux/kernel_layout.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

If you're curious about learning Linux kernel, you can check the GitHub link [here].(https://github.com/torvalds/linux)

#### What is a Linux Distribution?

By this point, you know that you can re-use the Linux kernel code, modify it, and create a new kernel. You can further combine different utilities and software to create a completely new operating system. A Linux distribution or distro is a version of the Linux operating system that includes the Linux kernel, system utilities, and other software. Being open source, a Linux distribution is a collaborative effort involving multiple independent open-source development communities. So, Linux distributions are different flavors of the Linux OS.

**_What does it mean that a distribution is derived?_**
When you say that a distribution is "derived" from another, the newer distro is built upon the base or foundation of the original distro. This derivation can include using the same package management system (more on this later), kernel version, and sometimes the same configuration tools. Today, there are thousands of Linux distributions to choose from, offering differing goals and criteria for selecting and supporting the software provided by their distribution.

Distributions vary from one to the other, but they generally have several common characteristics:

- A distribution consists of a Linux kernel.
- It supports user space programs.
- A distribution may be small and single-purpose or include thousands of open-source programs.
- Some means of installing and updating the distribution and its components should be provided.

If you view the [Linux Distributions Timeline](https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg), you'll see two major distros: Slackware and Debian. Several distributions are derived from them. For example, Ubuntu and Kali are derived from Debian.

There are various advantages of derivation. Derived distributions can leverage the stability, security, and large software repositories of the parent distribution.

When building on an existing foundation, developers can drive their focus and effort entirely on the specialized features of the new distribution. Users of derived distributions can benefit from the documentation, community support, and resources already available for the parent distribution.

Some popular Linux distributions are: `Ubuntu`, `Linux Mint`, `Arch Linux`, `Manjaro`, `Kali Linux` etc.

## Part 2: Introduction to Terminal, Console, Shell and System Commands

### 2.1. Introduction to Terminal, Console and Shell

#### What is a Terminal?

A terminal is a text-based user interface that allows users to interact with a computer by entering commands and viewing the results. A terminal can be connected to a local or a remote system, depending on the protocol of the network.

#### What is Console?

A console is a special type of terminal that is directly attached to the computer system. It is usually the primary interface for system administration and maintenance. It can be a physical device such as a keyboard and a monitor, or a virtual device such as a serial port or graphical window. A console gives you access to the low-level functions and settings of the computer system such as the BIOS, the boot loader or the kernel.
