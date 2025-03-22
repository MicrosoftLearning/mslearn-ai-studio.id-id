---
title: Mengembangkan solusi AI generatif
permalink: index.html
layout: home
---

# Mengembangkan solusi AI generatif di Azure

Latihan berikut dirancang untuk memberi Anda pengalaman pembelajaran praktis yang memungkinkan Anda menelusuri tugas-tugas umum yang dilakukan oleh pengembang saat membuat solusi AI generatif di Microsoft Azure.

> **Catatan**: Untuk menyelesaikan latihan, Anda memerlukan langganan Azure di mana Anda memiliki izin dan kuota yang memadai untuk menyediakan sumber daya Azure dan model AI generatif. Jika Anda belum memilikinya, Anda bisa mendaftar untuk mendapatkan [akun Azure](https://azure.microsoft.com/free). Ada opsi uji coba gratis untuk pengguna baru yang menyertakan kredit selama 30 hari pertama.

## Latihan

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})

{{activity.lab.description}}

{% endfor %}

> **Catatan**: Meskipun Anda dapat menyelesaikan latihan ini sendiri, latihan ini dirancang untuk melengkapi modul di [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/); di mana Anda akan menemukan penyelaman yang lebih dalam ke dalam beberapa konsep dasar yang menjadi konsep dasar latihan ini.
