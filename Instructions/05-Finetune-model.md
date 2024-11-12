---
lab:
  title: Menyempurnakan model bahasa untuk penyelesaian obrolan di Azure AI Studio
---

# Menyempurnakan model bahasa untuk penyelesaian obrolan di Azure AI Studio

Saat Anda ingin model bahasa berulah dengan cara tertentu, Anda dapat menggunakan rekayasa prompt untuk menentukan perilaku yang diinginkan. Ketika Anda ingin meningkatkan konsistensi perilaku yang diinginkan, Anda dapat memilih untuk menyempurnakan model, membandingkannya dengan pendekatan rekayasa prompt Anda untuk mengevaluasi metode mana yang paling sesuai dengan kebutuhan Anda.

Dalam latihan ini, Anda akan menyempurnakan model bahasa dengan Azure AI Studio yang ingin Anda gunakan untuk skenario salinan kustom. Anda akan membandingkan model yang disempurnakan dengan model dasar untuk menilai apakah model yang disempurnakan sesuai dengan kebutuhan Anda dengan lebih baik.

Bayangkan Anda bekerja untuk agen perjalanan dan Anda mengembangkan aplikasi obrolan untuk membantu orang merencanakan liburan mereka. Tujuannya adalah untuk membuat obrolan sederhana dan menginspirasi yang menyarankan tujuan dan aktivitas. Karena obrolan tidak terhubung ke sumber data apa pun, obrolan **tidak** boleh memberikan rekomendasi khusus untuk hotel, penerbangan, atau restoran untuk memastikan kepercayaan dengan pelanggan Anda.

Latihan ini akan memakan waktu sekitar **60** menit.

## Membuat hub dan proyek AI di Azure AI Studio

Anda mulai dengan membuat proyek Azure AI Studio dalam hub Azure AI:

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Pilih halaman **Beranda** lalu pilih **+ Proyek baru**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Hub**: *Buat proyek baru dengan pengaturan berikut:*
    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya baru*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-35-turbo** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Sambungkan Layanan Azure AI atau Azure OpenAI**: (Baru) *Autofills dengan nama hub yang Anda pilih*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum di pembantu lokasi mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Tinjau konfigurasi Anda dan buat proyek Anda.
1. Tunggu proyek Anda dibuat.

## Menyempurnakan model GPT-3.5

Karena menyempurnakan model membutuhkan waktu untuk diselesaikan, Anda akan memulai pekerjaan penyempurnaan terlebih dahulu. Sebelum Anda dapat menyempurnakan model, Anda memerlukan himpunan data.

1. Simpan himpunan data pelatihan sebagai file JSONL secara lokal: [https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl)

    > **Catatan**: Perangkat Anda mungkin default untuk menyimpan file sebagai file .txt. Pilih semua file dan hapus akhiran .txt untuk memastikan Anda menyimpan file sebagai JSONL.

1. Navigasikan ke halaman **Penyempurnaan** di bawah bagian **Alat** , menggunakan menu di sebelah kiri.
1. Pilih tombol untuk menambahkan model penyempurnaan baru, pilih `gpt-35-turbo` model, dan pilih **Konfirmasi**.
1. **Sempurnakan** model menggunakan konfigurasi berikut:
    - **Versi model**: *Pilih versi default*
    - **Akhiran model**: `ft-travel`
    - **Sambungan Azure OpenAI**: *Pilih koneksi default yang dibuat saat Anda membuat hub*
    - **Data pelatihan**: Mengunggah file

    <details>  
    <summary><b>Tips pemecahan masalah</b>: Kesalahan izin</summary>
    <p>Jika Anda menerima kesalahan izin saat membuat alur perintah baru, coba langkah berikut untuk memecahkan masalah:</p>
    <ul>
        <li>Di portal Azure, pilih Penjelajah Sumber Daya dari Semua Layanan.</li>
        <li>Pada halaman IAM, di tab Identitas, konfirmasikan bahwa itu adalah identitas terkelola yang ditetapkan sistem.</li>
        <li>Lakukan navigasi ke Akun Penyimpanan. Pada halaman IAM, tambahkan penetapan peran <em>Pembaca data blob penyimpanan</em>.</li>
        <li>Di bawah <strong>Tetapkan akses ke</strong>, pilih <strong>Identitas Terkelola</strong>, <strong>+ Pilih anggota</strong>, dan pilih <strong>Semua identitas terkelola yang ditetapkan sistem</strong>.</li>
        <li>Tinjau dan tetapkan untuk menyimpan pengaturan baru dan coba lagi langkah sebelumnya.</li>
    </ul>
    </details>

    - **Unggah file**: Pilih file JSONL yang Anda unduh di langkah sebelumnya.
    - **Data validasi**: Tidak ada
    - **Parameter tugas**: *Pertahankan pengaturan default*
1. Penyempurnaan akan dimulai dan mungkin perlu waktu untuk menyelesaikannya.

> **Catatan**: Penyempurnaan dan penyebaran dapat memakan waktu, jadi Anda mungkin perlu memeriksa kembali secara berkala untuk menyelesaikan langkah berikutnya. Anda sudah dapat melanjutkan dengan langkah berikutnya sembari menunggu.

## Mengobrol dengan model dasar

Saat Anda menunggu pekerjaan penyempurnaan selesai, mari kita mengobrol dengan model GPT 3.5 dasar untuk menilai performanya.

1. Navigasikan ke halaman **Penyebaran** di bawah bagian **Komponen** , menggunakan menu di sebelah kiri.
1. Pilih tombol **+ Sebarkan model** , dan pilih opsi **Sebarkan model dasar** .
1. Sebarkan `gpt-35-turbo` model, yang merupakan jenis model yang sama dengan yang Anda gunakan saat menyempurnakan.
1. Saat penyebaran selesai, navigasikan ke halaman **Obrolan** di bawah bagian **Proyek playground** .
1. Pilih model dasar yang Anda sebarkan `gpt-35-model` dalam penyebaran penyiapan.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan lihat responnya.
    Jawabannya sangat umum. Ingatlah bahwa kita ingin membuat aplikasi obrolan yang menginspirasi orang untuk bepergian.
1. Perbarui pesan sistem dengan perintah berikut:
    ```md
    You are an AI assistant that helps people plan their holidays.
    ```
1. Pilih **Simpan**, lalu pilih **Hapus obrolan**, dan tanyakan lagi `What can you do?` Sebagai respons, asisten dapat memberi tahu Anda bahwa itu dapat membantu Anda memesan penerbangan, hotel, dan mobil sewaan untuk perjalanan Anda. Anda ingin mengubah perilaku ini.
1. Perbarui pesan sistem lagi dengan perintah baru:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Pilih **Simpan**, dan **Hapus obrolan**.
1. Lanjutkan pengujian aplikasi obrolan Anda untuk memverifikasi bahwa aplikasi tersebut tidak memberikan informasi apa pun yang tidak di-grounded dalam data yang diambil. Misalnya, ajukan pertanyaan berikut dan jelajahi jawaban model:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    Model ini dapat memberi Anda daftar hotel, bahkan ketika Anda menginstruksikannya untuk tidak memberikan rekomendasi hotel. Ini adalah contoh perilaku yang tidak konsisten. Mari kita jelajahi apakah model yang disempurnakan berkinerja lebih baik dalam kasus ini.

1. Navigasikan ke halaman **Penyempurnaan** di bawah **Alat** untuk menemukan pekerjaan penyempurnaan Anda dan statusnya. Jika masih berjalan, Anda dapat memilih untuk terus mengevaluasi model dasar yang Anda sebarkan secara manual. Jika sudah selesai, Anda dapat melanjutkan dengan bagian berikutnya.

## Menyebarkan model yang disempurnakan

Saat penyempurnaan telah berhasil diselesaikan, Anda dapat menyebarkan model yang sudah disempurnakan.

1. Pilih model yang disempurnakan. Pilih tab **Metrik** dan jelajahi metrik penyempurnaan.
1. Sebarkan model yang disempurnakan dengan konfigurasi berikut:
    - **Nama penyebaran**: *Nama unik untuk model Anda, Anda dapat menggunakan default*
    - **Tipe penyebaran**: Standar
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default
1. Tunggu hingga penyebaran selesai sebelum Anda dapat mengujinya, ini mungkin memakan waktu cukup lama.

## Menguji model yang disempurnakan

Setelah menyebarkan model yang disempurnakan, Anda dapat menguji model seperti Anda dapat menguji model lain yang disebarkan.

1. Saat penyebaran siap, navigasikan ke model yang disempurnakan dan pilih **Buka di taman bermain**.
1. Perbarui pesan sistem dengan perintah berikut:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Uji model yang disempurnakan untuk menilai apakah perilakunya lebih konsisten sekarang. Misalnya, ajukan pertanyaan berikut lagi dan jelajahi jawaban model:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Penghapusan

Jika Anda telah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
