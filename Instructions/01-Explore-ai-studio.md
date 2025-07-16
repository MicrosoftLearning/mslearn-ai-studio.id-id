---
lab:
  title: Mempersiapkan proyek pengembangan AI
  description: Pelajari cara menata sumber daya cloud di hub dan proyek sehingga pengembang disiapkan agar sukses saat membangun solusi AI.
---

# Mempersiapkan proyek pengembangan AI

Dalam latihan ini, Anda menggunakan portal Azure AI Foundry untuk membuat pusat penyimpanan dan proyek, siap untuk membangun solusi AI.

Latihan ini memakan waktu sekitar **30** menit.

> **Catatan**: Beberapa teknologi yang digunakan dalam latihan ini sedang dalam pratinjau atau dalam pengembangan aktif. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan yang tidak terduga.

## Buka portal Azure AI Foundry

Mari kita mulai dengan menjelajahi portal Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Tinjau informasi di halaman beranda.

## Membuat proyek

*Proyek* Azure AI menyediakan ruang kerja kolaboratif untuk pengembangan AI. Mari kita mulai dengan memilih model yang ingin kita kerjakan dan membuat proyek untuk menggunakannya.

> **Catatan**: Proyek AI Foundry dapat didasarkan pada *sumber daya Azure AI Foundry*yang menyediakan akses ke model AI (termasuk Azure OpenAI), layanan Azure AI, dan sumber daya lainnya untuk mengembangkan agen AI dan solusi obrolan. Alternatifnya, proyek dapat didasarkan pada sumber daya *pusat penyimpanan AI*; yang mencakup koneksi ke sumber daya Azure untuk penyimpanan, komputasi, dan alat khusus yang aman. Proyek berbasis Azure AI Foundry sangat bagus untuk pengembang yang ingin mengelola sumber daya untuk agen AI atau pengembangan aplikasi obrolan. Proyek berbasis pusat penyimpanan AI lebih cocok untuk tim pengembangan perusahaan yang mengerjakan solusi AI yang kompleks.

1. Di beranda, pada bagian **Jelajahi model dan kemampuan** , cari`gpt-4o` model; yang akan kita gunakan dalam proyek kita.
1. Dalam hasil pencarian, pilih **model gpt-4o** untuk melihat detailnya, lalu di bagian atas halaman untuk model, pilih **Gunakan model** ini.
1. Saat diminta untuk membuat proyek, masukkan nama yang valid untuk proyek Anda dan perluas **opsi** Tingkat Lanjut.
1. Pilih **Sesuaikan** dan tentukan pengaturan berikut untuk proyek Anda:
    - **Sumber daya Azure AI Foundry**: *Nama yang valid untuk sumber daya Azure AI Foundry Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: *Pilih **Lokasi yang didukung Layanan AI***\*

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota model regional. Jika batas kuota terlampaui di kemudian hari dalam latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Buat** dan tunggu untuk proyek Anda, termasuk penyebaran model gpt-4 yang dipilih untuk dibuat.
1. Saat proyek Anda dibuat, ruang obrolan akan terbuka secara otomatis sehingga Anda dapat menguji model Anda:

    ![Cuplikan layar ruang obrolan proyek Azure AI Foundry.](./media/ai-foundry-chat-playground.png)

1. Di panel navigasi di sebelah kiri, pilih **Gambaran Umum** untuk melihat halaman utama untuk proyek Anda; yang terlihat seperti ini:

    ![Tangkapan layar halaman gambaran umum proyek Azure AI Foundry .](./media/ai-foundry-project.png)

1. Di bagian bawah panel navigasi di sebelah kiri, pilih **Pusat Manajemen**. Pusat manajemen adalah tempat Anda dapat mengonfigurasi pengaturan di tingkat *sumber daya* dan *proyek* ; yang keduanya diperlihatkan di panel navigasi.

    ![Tangkapan layar dari halaman Pusat Manajemen di portal Azure AI Foundry.](./media/ai-foundry-management.png)

    Tingkat *sumber daya* berkaitan dengan **sumber daya Azure AI Foundry** yang dibuat untuk mendukung proyek Anda. Sumber daya ini mencakup koneksi ke Azure AI Services dan model Azure AI Foundry; dan menyediakan pusat untuk mengelola akses pengguna ke proyek pengembangan AI.

    Tingkat *proyek* berkaitan dengan proyek individual Anda, tempat Anda dapat menambahkan dan mengelola sumber daya khusus proyek.

1. Di panel navigasi, di bagian untuk sumber daya Azure AI Foundry pilih halaman **Gambaran Umum** untuk melihat detailnya
1. Pilih tautan ke **grup Sumber daya**yang berhubungan dengan sumber daya untuk membuka halaman peramban baru dan melakukan navigasi ke portal Azure. Jika diminta, masuklah dengan informasi masuk Azure Anda.
1. Lihat grup sumber daya di portal Azure untuk melihat sumber daya Azure yang telah dibuat untuk mendukung sumber daya AI Foundry dan proyek Anda.

    ![Tangkapan layar pusat penyimpanan Azure AI Foundry dan sumber daya proyek di portal Azure.](./media/azure-portal-resources.png)

    Perhatikan bahwa sumber daya telah dibuat di wilayah yang Anda pilih saat membuat proyek tersebut.

1. Tutup halaman portal Azure dan kembali ke portal Azure AI Foundry.

## Meninjau koneksi proyek

Proyek Azure AI Foundry Anda dan sumber daya Azure AI Foundry yang dimilikinya menyertakan koneksi ke sumber daya yang dapat Anda gunakan dalam aplikasi AI.

1. Di halaman Pusat Manajeman, pada panel navigasi, di bawah proyek Anda, pilih **Buka sumber daya**.
1. Di halaman **Gambaran Umum** proyek, lihat **bagian Titik Akhir dan kunci**; yang berisi titik akhir dan kunci otorisasi yang dapat Anda gunakan dalam kode aplikasi Anda untuk mengakses:
    - Proyek Azure AI Foundry dan model apa pun yang disebarkan di dalamnya.
    - Azure OpenAI dalam model Azure AI Foundry.
    - Layanan Azure AI

## Menguji model AI generatif

Sekarang setelah Anda mengetahui sesuatu tentang konfigurasi proyek Azure AI Foundry, Anda dapat kembali ke ruang obrolan untuk menjelajahi model yang Anda sebarkan.

1. Di panel navigasi di sebelah kiri proyek Anda, pilih **Ruang obrolan**. 
1. Buka**Runag obrolan**dan pastikan bahwa model penyebaran**gpt-4o**dipilih dalam bagian **Penyebaran**.
1. Di panel **Penyiapan** , dalam kotak **Give the model instructions and context**, masukkan instruksi berikut ini:

    ```
   You are a history teacher who can answer questions about past events all around the world.
    ```

1. Terapkan perubahan untuk memperbarui pesan sistem.
1. Di jendela obrolan, masukkan kueri seperti `What are the key events in the history of Scotland?` dan lihat responsnya:

    ![Tangkapan layar playground di portal Azure AI Foundry.](./media/ai-foundry-playground.png)

## Ringkasan

Dalam latihan ini, Anda telah menjelajahi Azure AI Foundry, dan melihat cara membuat dan mengelola proyek dan sumber daya yang terkait dengannya.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Buka[Azure portal](https://portal.azure.com) lihat `https://portal.azure.com`konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
