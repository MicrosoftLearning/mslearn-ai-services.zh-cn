---
title: Azure AI 服务练习
permalink: index.html
layout: home
---

# Azure AI 服务练习

以下练习旨在支持 Microsoft Learn 上的模块。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %} {% unless activity.url contains 'ai-foundry' %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endunless %} {% endfor %}
