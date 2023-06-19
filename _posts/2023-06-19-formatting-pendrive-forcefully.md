---
layout: post
title: Formatting Infected Pendrive Forcefully.
date: 2023-06-19 08:00:00-0000
description: Tutorial post on formatting an external storage forcefully.
tags: misc
categories: tutorial
giscus_comments: true
related_posts: true
---

Sometimes when we connect our pendrive to a virus infected device, it gets infected. As a result, some files are created automatically inside the pendrive that cannot be deleted when we try to format it. Moreover, virus may also partition the drive the automatically. This problem can be solved by the following method.

- Go to search option and type `cmd`. You should see `Command Prompt` option in the dialog box. Right click on the option and select `Run as administrator`.

- On the command prompt window type `diskpart` which will turn on the `DISKPART` prompt.

- Type `list disk` on the DISKPART prompt. It should list all the available disks. Find out the drive you want to format. **_Note:_** Be careful when you select the drive. Once you perform the operation, it cannot be undone.

- Type `select disk disk_number` (e.g., select disk 1). The disk number represents the drive you want to format.

- Type `clean` and hit enter.

- After that type `create part primary` and hit enter and finally type `active` and hit enter.

Voila! Your pendrive should work just fine now. Exit the command prompt and check the pendrive again.
