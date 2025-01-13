---
layout: page
permalink: /research/
title: Research
description: <i>Feel free to visit my Google Scholar profile if the information here seems outdated.</i>
years: [2021]
nav: true
nav_order: 3
---

#### **Current Research Areas:**
- Motion Compensation in Free Breathing Pulmonary MRI
- Medical Imaging and Machine Learning
- Algorithm Development and Optimizations
- Inverse Problems and MR Image Reconstruction

#### **Research Interests:**

My research interests broadly lies in the area of Signal Processing and Deep Learning. Some of the areas I wish to work are:

- Audio Signal Processing, Signal Denoising, Speech Enhancement, Speech Recognition, Speaker Recognition, Speech Disorder, Speech Emotion Recognition, Speaker Diarization.
- Image Recognition, Image Processing, Medical Imaging, Image Segmentation.
- Sentiment and Emotion Analysis, QA Answering, Conversational AI.
- Multimodal Signal Processing (combining textual, acoustic & visual features) to build intelligent system.
- Audio Steganalysis, Audio Steganography, DeepFake Detecion.

<br />

#### **Publications:**

<!-- _pages/publications.md -->
<div class="publications">

{%- for y in page.years %}

  <h5 class="year">{{y}}</h5>

{% bibliography -f {{ site.scholar.bibliography }} -q @*[year={{y}}]* %}
{% endfor %}

</div>
