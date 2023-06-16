---
layout: page
permalink: /publications/
title: Publications
description: Visit my google scholar page if you find the date of last update of this site is long ago.
years: [2021]
nav: true
nav_order: 1
---

<!-- _pages/publications.md -->
<div class="publications">

{%- for y in page.years %}

  <h2 class="year">{{y}}</h2>
  {% bibliography -f {{ site.scholar.bibliography }} -q @*[year={{y}}]* %}
{% endfor %}

</div>
