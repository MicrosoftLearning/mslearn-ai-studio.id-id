---
lab:
  title: 'Menjelajahi, menyebarkan, dan mengobrol dengan model bahasa di Azure AI Studio'
---

# Menjelajahi, menyebarkan, dan mengobrol dengan model bahasa di Azure AI Studio

Katalog model Azure AI Studio berfungsi sebagai repositori pusat tempat Anda dapat menjelajahi dan menggunakan berbagai model, memfasilitasi pembuatan skenario AI generatif Anda.

Dalam latihan ini, Anda akan menjelajahi katalog model di Azure AI Studio.

Latihan ini akan memakan waktu sekitar **25** menit.

## Membuat Azure AI hub

Anda memerlukan hub Azure AI di langganan Azure Anda untuk menghosting proyek. Anda dapat membuat sumber daya ini saat membuat proyek, atau menyediakannya sebelumnya (yang akan kita lakukan dalam latihan ini).

1. Pada bagian **Manajemen**, pilih **Semua sumber daya**, lalu pilih **+ Hub baru**. Buat proyek baru dengan pengaturan berikut:
    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya baru*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-35-turbo** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Sambungkan Layanan Azure AI atau Azure OpenAI**: (Baru) *Autofills dengan nama hub yang Anda pilih*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum di pembantu lokasi mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

    Setelah hub Azure AI dibuat, hub tersebut akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar detail hub Azure AI di Azure AI Studio.](./media/azure-ai-resource.png)

1. Buka tab browser baru (biarkan tab Azure AI Studio terbuka) dan telusuri ke portal Azure di [https://portal.azure.com](https://portal.azure.com?azure-portal=true), masuk dengan kredensial Azure Anda jika diminta.
1. Telusuri ke grup sumber daya tempat Anda membuat hub Azure AI, dan lihat sumber daya Azure yang telah dibuat.

    ![Cuplikan layar hub Azure AI dan sumber daya terkait di portal Azure.](./media/azure-portal.png)

1. Kembali ke tab browser Azure AI Studio.
1. Tampilkan setiap halaman di panel di sisi kiri halaman untuk hub Azure AI Anda, dan perhatikan artefak yang bisa Anda buat dan kelola. Pada halaman **Koneksi** , amati bahwa koneksi ke Layanan Azure OpenAI dan AI telah dibuat.

## Membuat proyek

Hub Azure AI menyediakan ruang kerja kolaboratif tempat Anda dapat menentukan satu atau beberapa *proyek*. Mari kita buat proyek di hub Azure AI Anda.

1. Di Azure AI Studio, pastikan Anda berada di hub yang baru saja Anda buat (Anda dapat memverifikasi lokasi Anda dengan memeriksa jalur di bagian atas layar).
1. Navigasi ke **Semua proyek** menggunakan menu di sebelah kiri.
1. Pilih **+ Proyek baru**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Hub saat ini**: *Hub AI Anda*
    - **Nama proyek**: *Nama unik untuk proyek Anda*
1. Tunggu proyek Anda dibuat. Halaman ini akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar halaman detail proyek di portal Azure AI Studio.](./media/azure-ai-project.png)

1. Tampilkan halaman di panel di sisi kiri, perluas setiap bagian, dan perhatikan tugas yang bisa Anda lakukan dan sumber daya yang bisa Anda kelola dalam proyek.

## Memilih model menggunakan tolok ukur model

Sebelum menyebarkan model, Anda dapat menjelajahi tolok ukur model untuk memutuskan model mana yang paling sesuai dengan kebutuhan Anda.

Bayangkan Anda ingin membuat salinan kustom yang berfungsi sebagai asisten perjalanan. Secara khusus, Anda ingin salinan Anda menawarkan dukungan untuk pertanyaan terkait perjalanan, seperti persyaratan visa, prakiraan cuaca, atraksi lokal, dan norma budaya.

Salinan Anda perlu memberikan informasi yang akurat secara faktual, jadi groundedness penting. Selanjutnya, Anda ingin jawaban copilot mudah dibaca dan dipahami. Oleh karena itu, Anda juga ingin memilih model yang memiliki tingkat tinggi pada kefasihan dan koherensi.

1. Di Azure AI Studio, navigasikan ke **Tolok ukur Model** di bawah bagian **Memulai** , menggunakan menu di sebelah kiri.
    Di tab **Tolok ukur kualitas** Anda dapat menemukan beberapa bagan yang sudah divisualisasikan untuk Anda, membandingkan model yang berbeda.
1. Filter model yang ditampilkan:
    - **Tugas**: Jawaban atas pertanyaan
    - **Koleksi**: Azure OpenAI
    - **Metrik**: Koherensi, Kefasihan, Groundedness
1. Jelajahi bagan yang dihasilkan dan tabel perbandingan. Saat menjelajahi, Anda dapat mencoba dan menjawab pertanyaan berikut:
    - Apakah Anda melihat perbedaan performa antara model GPT-3.5 dan GPT-4?
    - Apakah ada perbedaan antara versi model yang sama?
    - Bagaimana varian 32k berbeda dari model dasar?

Dari koleksi Azure OpenAI, Anda dapat memilih antara model GPT-3.5 dan GPT-4. Mari kita sebarkan kedua model ini dan jelajahi bagaimana perbandingannya untuk kasus penggunaan Anda.

## Menyebarkan model Azure OpenAI

Sekarang setelah Anda menjelajahi opsi Anda melalui tolok ukur model, Anda siap untuk menyebarkan model bahasa. Anda dapat menelusuri katalog model, dan menyebarkan dari sana, atau Anda dapat menyebarkan model melalui halaman **Penyebaran** . Mari kita jelajahi kedua opsi tersebut.

### Menyebarkan model dari katalog Model

Mari kita mulai dengan menyebarkan model dari katalog Model. Anda mungkin lebih suka opsi ini ketika Anda ingin memfilter semua model yang tersedia.

1. Navigasikan ke halaman **Katalog model** di bawah bagian **Memulai** , menggunakan menu di sebelah kiri.
1. Cari dan sebarkan `gpt-35-turbo` model, yang dikumpulkan oleh Azure AI, dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

### Menyebarkan model melalui Penyebaran

Jika Anda sudah tahu persis model mana yang ingin Anda sebarkan, Anda mungkin lebih suka melakukannya melalui Penyebaran.

1. Navigasikan ke halaman **Penyebaran** di bawah bagian **Komponen** , menggunakan menu di sebelah kiri.
1. Pada tab **Penyebaran model** ,buat penyebaran model baru dengan pengaturan berikut:
    - **Model**: gpt 4
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > **Catatan**: Anda mungkin telah melihat beberapa model yang menunjukkan tolok ukur Model, tetapi bukan sebagai opsi dalam katalog model Anda. Ketersediaan model berbeda per lokasi. Lokasi Anda ditentukan pada tingkat hub AI, tempat Anda dapat menggunakan **Pembantu lokasi** untuk menentukan model yang ingin Anda sebarkan untuk mendapatkan daftar lokasi tempat Anda dapat menyebarkannya.

## Menguji model di taman bermain obrolan

Sekarang setelah kita memiliki dua model untuk dibandingkan, mari kita lihat bagaimana model bersikap dalam interaksi percakapan.

1. Navigasikan ke halaman **Obrolan** di bawah bagian **Proyek playground** , menggunakan menu di sebelah kiri.
1. Di **Taman bermain obrolan**, pilih penyebaran GPT-3.5 Anda.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan lihat responnya.
    Jawabannya sangat umum. Ingatlah kita ingin membuat salinan kustom yang berfungsi sebagai asisten perjalanan. Anda dapat menentukan jenis bantuan apa yang Anda inginkan dalam pertanyaan yang Anda ajukan.
1. Di jendela obrolan, masukkan kueri `Imagine you're a travel assistant, what can you help me with?` Jawabannya sudah lebih spesifik. Anda mungkin tidak ingin pengguna akhir Anda harus memberikan konteks yang diperlukan setiap kali mereka berinteraksi dengan salinan Anda. Untuk menambahkan instruksi global, Anda dapat mengedit pesan sistem.
1. Perbarui pesan sistem dengan perintah berikut:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Pilih **Simpan**, dan **Hapus obrolan**.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan tampilkan respons baru. Amati perbedaannya dengan jawaban yang Anda terima sebelumnya. Jawabannya khusus untuk perjalanan sekarang.
1. Lanjutkan percakapan dengan bertanya: `I'm planning a trip to London, what can I do there?` Salinan menawarkan banyak informasi terkait perjalanan. Anda mungkin ingin memperbaiki outputnya. Misalnya, Anda mungkin ingin jawabannya lebih ringkas.
1. Perbarui pesan sistem dengan menambahkan `Answer with a maximum of two sentences.` ke akhir pesan. Terapkan perubahan, hapus obrolan, dan uji obrolan lagi dengan bertanya: `I'm planning a trip to London, what can I do there?` Anda mungkin juga ingin salinan Anda melanjutkan percakapan alih-alih hanya menjawab pertanyaan.
1. Perbarui pesan sistem dengan menambahkan `End your answer with a follow-up question.` ke akhir pesan. Simpan perubahan, kosongkan obrolan, dan uji obrolan lagi dengan bertanya: `I'm planning a trip to London, what can I do there?`
1. Ubah **Penyebaran** Anda ke model GPT-4 Anda dan ulangi semua langkah di bagian ini. Perhatikan bagaimana model dapat bervariasi dalam outputnya.
1. Terakhir, uji kedua model pada kueri `Who is the prime minister of the UK?`. Performa pada pertanyaan ini terkait dengan groundedness (apakah responsnya akurat secara faktual) dari model. Apakah performa berkorelasi dengan kesimpulan Anda dari tolok ukur Model?

Sekarang setelah Anda menjelajahi kedua model, pertimbangkan model apa yang akan Anda pilih sekarang untuk kasus penggunaan Anda. Pada awalnya, output dari model mungkin berbeda, dan Anda mungkin lebih suka satu model daripada yang lain. Namun, setelah memperbarui pesan sistem, Anda mungkin melihat bahwa perbedaannya minimal. Dari perspektif pengoptimalan biaya, Anda kemudian dapat memilih model GPT-3.5 daripada model GPT-4, karena performanya sangat mirip.

## Penghapusan

Setelah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com?azure-portal=true) di tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
