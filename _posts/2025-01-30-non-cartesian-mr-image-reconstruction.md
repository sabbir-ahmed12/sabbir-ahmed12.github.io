---
layout: post
title: Non-Cartesian MR Image Reconstruction
date: 2025-01-30 09:24:00-0400
description: Tutorial post on non-cartesian MR image reconstruction.
tags: image
categories: tutorial
giscus_comments: true
related_posts: false
---

{::nomarkdown}
{% assign jupyter_path = 'assets/jupyter/test.ipynb' | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/test.ipynb %}{% endcapture %}
{% if notebook_exists == 'true' %}
{% jupyter_notebook jupyter_path %}
{% else %}

  <p>Sorry, the notebook you are looking for does not exist.</p>
{% endif %}
{:/nomarkdown}
