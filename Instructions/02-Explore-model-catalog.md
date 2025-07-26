---
lab:
  title: Memilih dan menyebarkan model bahasa
  description: Aplikasi AI generatif dibangun di atas satu atau beberapa model bahasa. Pelajari cara menemukan dan memilih model yang tepat untuk proyek AI generatif Anda.
---

# Memilih dan menyebarkan model bahasa

Katalog model Azure AI Foundry berfungsi sebagai repositori pusat tempat Anda dapat menjelajahi dan menggunakan berbagai model, memfasilitasi pembuatan skenario AI generatif Anda.

Dalam latihan ini, Anda akan menjelajahi katalog model di portal Azure AI Foundry, dan membandingkan model potensial untuk aplikasi AI generatif yang membantu memecahkan masalah.

Latihan ini akan memakan waktu sekitar **25** menit.

> **Catatan**: Beberapa teknologi yang digunakan dalam latihan ini sedang dalam pratinjau atau dalam pengembangan aktif. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan yang tidak terduga.

## Menjelajahi model

Mari kita mulai dengan masuk ke portal Azure AI Foundry dan menjelajahi beberapa model yang tersedia.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Tinjau informasi di halaman beranda.
1. Di beranda, pada bagian **Jelajahi model dan kemampuan** , cari`gpt-4o` model; yang akan kita gunakan dalam proyek kita.
1. Dalam hasil pencarian, pilih **model gpt-4o** untuk melihat detailnya.
1. Baca deskripsi dan tinjau informasi lain yang tersedia di **Detail** halaman.

    ![Tangkapan layar halaman detail model gpt-4o.](./media/gpt4-details.png)

1. Pada halaman **gpt-4o**, lihat tab **Benchmarks** untuk melihat bagaimana model tersebut dibandingkan pada beberapa tolak ukur performa standar dengan model lain yang digunakan dalam skenario serupa.

    ![Tangkapan layar halaman tolak ukur model gpt-4o.](./media/gpt4-benchmarks.png)

1. Gunakan panah belakang (**&larr;**) di samping judul halaman **gpt-4o** untuk kembali ke beranda katalog model.
1. Cari `Phi-4-mini-instruct` dan lihat detail dan tolak ukur untuk model **Phi-4-mini-instruct**.

## Membandingkan beberapa model

Anda telah meninjau dua model berbeda, yang keduanya dapat digunakan untuk mengimplementasikan aplikasi obrolan AI generatif. Sekarang mari kita bandingkan metrik untuk kedua model ini secara visual.

1. Gunakan panah kembali (**&larr;**) untuk kembali ke katalog model.
1. Pilih **Bandingkan model**. Bagan visual untuk perbandingan model ditampilkan dengan pilihan model umum.

    ![Tangkapan layar halaman perbandingan model.](./media/compare-models.png)

1. Di panel **Model untuk dibandingkan** di sebelah kiri, perhatikan bahwa Anda bisa memilih tugas populer, seperti *jawaban atas pertanyaan* untuk memilih model yang umum digunakan secara otomatis untuk tugas tertentu.
1. Gunakan ikon **Hapus semua model** (&#128465;) untuk menghapus semua model yang telah dipilih sebelumnya.
1. Gunakan tombol **+ Model untuk membandingkan** untuk menambahkan model **gpt-4o** ke dalam daftar. Kemudian gunakan tombol yang sama untuk menambahkan model **Phi-4-mini-instruct** ke daftar.
1. Tinjau bagan, yang membandingkan model berdasarkan **Indeks Kualitas** (skor standar yang menunjukkan kualitas model) dan **Biaya**. Anda dapat melihat nilai tertentu untuk model dengan menahan mouse di atas titik yang mewakilinya dalam bagan.

    ![Cuplikan layar bagan perbandingan model untuk gpt-4o dan Phi-4-mini-instruct.](./media/comparison-chart.png)

1. Di menu dropdown **Sumbu-X**, di bawah **Kualitas**, pilih metrik berikut dan amati setiap bagan yang dihasilkan sebelum beralih ke bagan berikutnya:
    - Akurasi
    - Indeks kualitas

    Berdasarkan tolok ukur, model gpt-4o terlihat seperti menawarkan performa keseluruhan terbaik, tetapi dengan biaya yang lebih tinggi.

1. Dalam daftar model yang akan dibandingkan, pilih **model gpt-4o** untuk membuka kembali halaman tolok ukurnya.
1. Di halaman untuk **halaman model gpt-4o** , pilih **halaman Gambaran Umum** untuk melihat detail model.

## Membuat proyek Azure OpenAI

Untuk menggunakan model, Anda perlu membuat proyek* Azure AI Foundry*.

1. Di bagian atas halaman gambaran **umum model gpt-4o** , pilih **Gunakan model** ini.
1. Saat diminta untuk membuat proyek, masukkan nama yang valid untuk proyek Anda dan perluas **opsi** Tingkat Lanjut.
1. Di bagian **Opsi tingkat lanjut** tentukan pengaturan berikut untuk proyek Anda:
    - **Sumber daya Azure AI Foundry**: *Nama yang valid untuk sumber daya Azure AI Foundry Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: *Pilih **Lokasi yang didukung Layanan AI***\*

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota model regional. Jika batas kuota terlampaui di kemudian hari dalam latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Buat** dan tunggu untuk proyek Anda, termasuk penyebaran model gpt-4 yang dipilih untuk dibuat.
1. Saat proyek Anda dibuat, ruang obrolan akan terbuka secara otomatis sehingga Anda dapat menguji model Anda:

    ![Cuplikan layar ruang obrolan proyek Azure AI Foundry.](./media/ai-foundry-chat-playground.png)

## Mengobrol dengan model *gpt-4o*

Sekarang setelah Anda memiliki penyebaran model, Anda dapat menggunakan ruang obrolan untuk mengujinya.

1. Di ruang obrolan, di panel **Penyiapan**, pastikan bahwa model gpt-4o** Anda **model dipilih dan di dalam bidang **Berikan instruksi model dan konteks** atur perintah sistem ke`You are an AI assistant that helps solve problems.`
1. Pilih **Terapkan perubahan** untuk memperbarui perintah sistem.

1. Di jendela kueri, masukkan kueri berikut ini

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Tampilkan responsnya. Lalu masukkan kueri susulan berikut:

    ```
   Explain your reasoning.
    ```

## Sebarkan model baru

Saat Anda membuat proyek, **model gpt-4o** yang Anda pilih secara otomatis disebarkan. Mari kita sebarkan model ***Phi-4-mini-instruct** yang juga Anda pertimbangkan.

1. Di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Di tab **Penyebaran model**, di daftar drop-down **+ Sebarkan model**, pilih **Sebarkan model dasar**. Kemudian cari `Phi-4-mini-instruct` dan konfirmasikan pilihan Anda.
1. Setujui lisensi model.
1. Sebarkan model **Phi-4-mini-instruct** dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama yang valid untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar Global
    - **Detail penyebaran**: *Gunakan pengaturan default*

1. Tunggu hingga penerapan selesai.

## Mengobrol dengan model *Phi-4*

Sekarang mari kita mengobrol dengan model baru di ruang obrolan.

1. Di bilah navigasi, pilih **Playground**. Lalu pilih **Playground obrolan**.
1. Di playground obrolan, di panel **Penyiapan**, pastikan bahwa model **Phi-4-mini-instruct** Anda dipilih dan di kotak obrolan, berikan baris pertama sebagai `System message: You are an AI assistant that helps solve problems.` (permintaan sistem yang sama dengan yang Anda gunakan untuk menguji model gpt-4o, tetapi karena tidak ada pengaturan pesan sistem, kami menyediakannya di obrolan pertama untuk konteks.)
1. Pada baris baru di jendela obrolan (di bawah pesan sistem Anda), masukkan kueri berikut

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Tampilkan responsnya. Lalu masukkan kueri susulan berikut:

    ```
   Explain your reasoning.
    ```

## Melakukan perbandingan lebih lanjut

1. Gunakan daftar drop-down di panel **Penyiapan** untuk beralih di antara model Anda, menguji kedua model dengan teka-teki berikut (jawaban yang benar adalah 40!):

    ```
   I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Cermati model-modelnya

Anda telah membandingkan dua model, yang mungkin bervariasi dalam hal kemampuannya untuk menghasilkan respons yang sesuai dan biayanya. Dalam skenario generatif apa pun, Anda perlu menemukan model dengan keseimbangan yang tepat antara kesesuaian untuk tugas yang perlu Anda lakukan dan biaya penggunaan model untuk jumlah permintaan yang Anda harapkan harus ditangani.

Detail dan tolak ukur yang disediakan dalam katalog model, bersama dengan kemampuan untuk membandingkan model secara visual memberikan titik awal yang berguna saat mengidentifikasi model kandidat untuk solusi AI generatif. Anda kemudian dapat menguji model kandidat dengan berbagai perintah sistem dan pengguna di playground obrolan.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Buka [portal Azure](https://portal.azure.com) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
