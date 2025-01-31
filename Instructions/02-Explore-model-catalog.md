---
lab:
  title: 'Menjelajahi, menerapkan, dan mengobrol dengan model bahasa di Azure AI Foundry'
---

# Menjelajahi, menerapkan, dan mengobrol dengan model bahasa di Azure AI Foundry

Katalog model Azure AI Foundry berfungsi sebagai repositori pusat tempat Anda dapat menjelajahi dan menggunakan berbagai model, memfasilitasi pembuatan skenario AI generatif Anda.

Dalam latihan ini, Anda akan menjelajahi katalog model di portal Azure AI Foundry.

Latihan ini akan memakan waktu sekitar **25** menit.

## Membuat hub dan proyek Azure AI

Hub Azure AI menyediakan ruang kerja kolaboratif tempat Anda dapat menentukan satu atau beberapa *proyek*. Mari kita buat proyek dan hub Azure AI.

1. Di browser web, buka portal [Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda.

1. Di beranda, pilih **+ Buat proyek**. Di wizard **Buat proyek**, Anda bisa melihat semua sumber daya Azure yang akan dibuat secara otomatis dengan proyek Anda, atau Anda bisa mengkustomisasi pengaturan berikut dengan memilih **Sesuaikan** sebelum memilih **Buat**:

    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya baru*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-35-turbo** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Sambungkan Layanan Azure AI atau Azure OpenAI**: (Baru) *Autofills dengan nama hub yang Anda pilih*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum di pembantu lokasi mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Jika Anda memilih **Sesuaikan**, pilih **Berikutnya** dan tinjau konfigurasi Anda.
1. Pilih **Buat** dan tunggu hingga prosesnya selesai.
   
    Setelah hub dan proyek Azure AI dibuat, tampilannya akan terlihat seperti gambar berikut:

    ![Tangkapan layar detail hub Azure AI di portal Azure AI Foundry.](./media/azure-ai-resource.png)

1. Buka tab browser baru (biarkan tab Azure AI Foundry terbuka) dan telusuri ke portal Azure di [https://portal.azure.com](https://portal.azure.com?azure-portal=true), masuk dengan kredensial Azure Anda jika diminta.
1. Telusuri ke grup sumber daya tempat Anda membuat hub Azure AI, dan lihat sumber daya Azure yang telah dibuat.

    ![Cuplikan layar hub Azure AI dan sumber daya terkait di portal Azure.](./media/azure-portal.png)

1. Kembali ke tab browser portal Azure AI Foundry.
1. Tampilkan setiap halaman di panel di sisi kiri halaman untuk hub Azure AI Anda, dan perhatikan artefak yang bisa Anda buat dan kelola. Di halaman **Pusat manajemen**, Anda dapat memilih **Sumber daya terhubung**, baik di bawah hub atau proyek Anda, dan mengamati bahwa koneksi ke Layanan Azure OpenAI dan AI telah dibuat.
1. Jika Anda berada di halaman Pusat manajemen, pilih **Buka proyek**.

## Memilih model menggunakan tolok ukur model

Sebelum menyebarkan model, Anda dapat menjelajahi tolok ukur model untuk memutuskan model mana yang paling sesuai dengan kebutuhan Anda.

Bayangkan Anda ingin membuat salinan kustom yang berfungsi sebagai asisten perjalanan. Secara khusus, Anda ingin salinan Anda menawarkan dukungan untuk pertanyaan terkait perjalanan, seperti persyaratan visa, prakiraan cuaca, atraksi lokal, dan norma budaya.

Salinan Anda perlu memberikan informasi yang akurat secara faktual, jadi groundedness penting. Selanjutnya, Anda ingin jawaban copilot mudah dibaca dan dipahami. Oleh karena itu, Anda juga ingin memilih model yang memiliki peringkat tinggi pada kefasihan dan koherensi.

1. Di portal proyek Azure AI Foundry, navigasikan ke **katalog Model** menggunakan menu di sebelah kiri.
    Di halaman katalog, pilih **Bandingkan dengan tolok ukur**. Di halaman Tolok ukur model, Anda akan menemukan bagan yang sudah diplot untuk Anda, membandingkan model yang berbeda.
1. Pilih **+ Model untuk membandingkan** dan menambahkan **gpt-4-32k** dan **gpt-4** ke bagan metrik. Di menu dropdown **Sumbu-X**, di bawah **Kualitas**, pilih metrik berikut dan amati setiap bagan yang dihasilkan sebelum beralih ke bagan berikutnya:
    - Koherensi
    - Kelancaran
    - Groundedness
1. Saat menjelajahi hasilnya, Anda dapat mencoba dan menjawab pertanyaan berikut:
    - Apakah Anda melihat perbedaan performa antara model GPT-3.5 dan GPT-4?
    - Apakah ada perbedaan antara versi model yang sama?
    - Bagaimana varian 32k GPT-4 berbeda dari model dasar?

Dari koleksi Azure OpenAI, Anda dapat memilih antara model GPT-3.5 dan GPT-4. Mari kita sebarkan kedua model ini dan jelajahi bagaimana perbandingannya untuk kasus penggunaan Anda.

## Menyebarkan model Azure OpenAI

Sekarang setelah Anda menjelajahi opsi Anda melalui tolok ukur model, Anda siap untuk menyebarkan model bahasa. Anda dapat menelusuri katalog model, dan menyebarkan dari sana, atau Anda dapat menyebarkan model melalui halaman **Penyebaran** . Mari kita jelajahi kedua opsi tersebut.

### Menyebarkan model dari katalog Model

Mari kita mulai dengan menyebarkan model dari katalog Model. Anda mungkin lebih suka opsi ini ketika Anda ingin memfilter semua model yang tersedia.

1. Navigasikan ke halaman **Katalog model** menggunakan menu di sebelah kiri.
1. Cari dan terapkan `gpt-35-turbo`model, yang dikumpulkan oleh Azure AI, dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penerapan:
   
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > **Catatan**: Jika lokasi sumber daya AI Anda saat ini tidak memiliki kuota yang tersedia untuk model yang ingin Anda terapkan, Anda akan diminta untuk memilih lokasi lain tempat sumber daya AI baru akan dibuat dan tersambung ke proyek Anda.

### Menerapkan model melalui Model + titik akhir

Jika Anda sudah mengetahui dengan pasti model mana yang ingin Anda terapkan, Anda mungkin lebih suka melakukannya melalui **Models + titik akhir**.

1. Navigasikan ke halaman **Model + titik akhir** di bawah bagian **Aset saya**, menggunakan menu di sebelah kiri.
1. Di tab **Penyebaran model**, terapkan model dasar baru dengan pengaturan berikut dengan memilih **Kustomisasi** dalam detail penyebaran:
    - **Model**: gpt 4
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

## Menguji model di taman bermain obrolan

Sekarang setelah kita memiliki dua model untuk dibandingkan, mari kita lihat bagaimana model bersikap dalam interaksi percakapan.

1. Navigasi ke halaman**Playgrounds** menggunakan menu di sebelah kiri.
1. Di **Taman bermain obrolan**, pilih penyebaran GPT-3.5 Anda.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan lihat responnya.
    Jawabannya sangat umum. Ingatlah kita ingin membuat salinan kustom yang berfungsi sebagai asisten perjalanan. Anda dapat menentukan jenis bantuan apa yang Anda inginkan dalam pertanyaan yang Anda ajukan.
1. Di jendela obrolan, masukkan kueri `Imagine you're a travel assistant, what can you help me with?` Jawabannya sudah lebih spesifik. Anda mungkin tidak ingin pengguna akhir Anda harus memberikan konteks yang diperlukan setiap kali mereka berinteraksi dengan salinan Anda. Untuk menambahkan instruksi global, Anda dapat mengedit pesan sistem.
1. Di bawah **Pengaturan**, perbarui bidang **Berikan instruksi dan konteks kepada model** dengan perintah berikut:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Pilih **Terapkan perubahan**.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan tampilkan respons baru. Amati perbedaannya dengan jawaban yang Anda terima sebelumnya. Jawabannya khusus untuk perjalanan sekarang.
1. Lanjutkan percakapan dengan bertanya: `I'm planning a trip to London, what can I do there?` Salinan menawarkan banyak informasi terkait perjalanan. Anda mungkin ingin memperbaiki outputnya. Misalnya, Anda mungkin ingin jawabannya lebih ringkas.
1. Perbarui pesan sistem dengan menambahkan `Answer with a maximum of two sentences.` ke akhir pesan. Terapkan perubahan, hapus obrolan, dan uji obrolan lagi dengan bertanya: `I'm planning a trip to London, what can I do there?` Anda mungkin juga ingin salinan Anda melanjutkan percakapan alih-alih hanya menjawab pertanyaan.
1. Perbarui konteks model dengan menambahkan `End your answer with a follow-up question.` ke akhir perintah. Simpan perubahan dan uji obrolan lagi dengan bertanya: `I'm planning a trip to London, what can I do there?`
1. Ubah **Penyebaran** Anda ke model GPT-4 Anda dan ulangi semua langkah di bagian ini. Perhatikan bagaimana model dapat bervariasi dalam outputnya.
1. Terakhir, uji kedua model pada kueri `Who is the prime minister of the UK?`. Performa pada pertanyaan ini terkait dengan groundedness (apakah responsnya akurat secara faktual) dari model. Apakah performa berkorelasi dengan kesimpulan Anda dari tolok ukur Model?

Sekarang setelah Anda menjelajahi kedua model, pertimbangkan model apa yang akan Anda pilih sekarang untuk kasus penggunaan Anda. Pada awalnya, output dari model mungkin berbeda, dan Anda mungkin lebih suka satu model daripada yang lain. Namun, setelah memperbarui pesan sistem, Anda mungkin melihat bahwa perbedaannya minimal. Dari perspektif pengoptimalan biaya, Anda kemudian dapat memilih model GPT-3.5 daripada model GPT-4, karena performanya sangat mirip.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com?azure-portal=true) di tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
