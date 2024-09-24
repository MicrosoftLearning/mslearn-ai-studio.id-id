---
title: Latihan Azure OpenAI
permalink: index.html
layout: home
---

# Latihan Azure AI Studio

Latihan berikut dirancang untuk mendukung modul di [Microsoft Learn](https://learn.microsoft.com/training).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}