---
title: Latihan Azure OpenAI
permalink: index.html
layout: home
---

# Mengembangkan aplikasi AI generatif di portal Azure AI Foundry

Latihan berikut dirancang untuk memberi Anda pengalaman pembelajaran langsung di mana Anda akan menjelajahi pola dan teknik umum yang digunakan pengembang untuk membangun aplikasi AI generatif seperti "copilot", dan mempelajari cara menerapkan pola tersebut menggunakan Layanan Azure AI - khususnya, Layanan Azure OpenAI dan Azure AI Studio.

Meskipun Anda dapat menyelesaikan latihan ini sendiri, latihan ini dirancang untuk melengkapi modul di [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/); di mana Anda akan menemukan penyelaman yang lebih dalam ke dalam beberapa konsep yang mendasar di mana latihan ini didasarkan.

> **Catatan**: Untuk menyelesaikan latihan, Anda memerlukan langganan Azure di mana Anda memiliki izin dan kuota yang memadai untuk menyediakan sumber daya Azure yang digunakan oleh Azure AI Studio dan untuk menyebarkan dan menggunakan model GPT Azure OpenAI. Jika Anda belum memilikinya, Anda bisa mendaftar untuk mendapatkan [akun Azure](https://azure.microsoft.com/free). Ada opsi uji coba gratis untuk pengguna baru yang menyertakan kredit selama 30 hari pertama.

## Latihan

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}