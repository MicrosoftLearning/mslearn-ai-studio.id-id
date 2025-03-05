---
lab:
  title: Menyempurnakan model bahasa
  description: Pelajari cara menggunakan data pelatihan tambahan Anda sendiri untuk menyempurnakan model dan menyesuaikan perilakunya.
---

# Menyempurnakan model bahasa

Saat Anda ingin model bahasa berperilaku dengan gaya tertentu, Anda dapat menggunakan rekayasa perintah untuk menentukan perilaku yang diinginkan. Ketika Anda ingin meningkatkan konsistensi atas perilaku yang diinginkan, Anda dapat memilih untuk menyempurnakan model, membandingkannya dengan pendekatan rekayasa prompt Anda untuk mengevaluasi metode mana yang paling sesuai dengan kebutuhan Anda.

Dalam latihan ini, Anda akan menyempurnakan model bahasa dengan Azure AI Foundry yang ingin Anda gunakan untuk skenario aplikasi percakapan yang disesuaikan. Anda akan membandingkan model yang disempurnakan dengan model dasar untuk menilai apakah model yang disempurnakan lebih sesuai dengan kebutuhan Anda.

Bayangkan Anda bekerja untuk agen perjalanan dan Anda mengembangkan aplikasi obrolan untuk membantu orang merencanakan liburan mereka. Tujuannya adalah untuk membuat obrolan sederhana dan menginspirasi yang menyarankan tujuan dan aktivitas. Karena obrolan tidak terhubung ke sumber data apa pun, obrolan **tidak** boleh memberikan rekomendasi khusus untuk hotel, penerbangan, atau restoran untuk memastikan kepercayaan pelanggan Anda.

Latihan ini akan memakan waktu sekitar **60** menit\*.

> \***Catatan**: Waktu ini adalah perkiraan berdasarkan pengalaman rata-rata. Penyempurnaan tergantung pada sumber daya infrastruktur cloud, yang dapat memakan waktu yang bervariasi untuk disediakan tergantung pada kapasitas pusat data dan permintaan bersamaan. Beberapa aktivitas dalam latihan ini mungkin membutuhkan waktu yang <u>lama</u> untuk diselesaikan, dan memerlukan kesabaran. Jika ada yang memakan waktu cukup lama, pertimbangkan untuk meninjau [Dokumentasi penyempurnaan Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) atau beristirahat.

## Membuat AI hub dan proyek di portal Azure AI Foundry.

Anda mulai dengan membuat proyek portal Azure AI Foundry dalam hub Azure AI:

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Dari halaman beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - Pilih **Sesuaikan**
        - **Hub**: *Isi otomatis dengan nama default*
        - **Langganan**: *Isi otomatis dengan akun masuk Anda*
        - **Grup sumber daya**: (Baru) *Isi otomatis dengan nama proyek Anda*
        -  **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-4-finetune** pada jendela pembantu Lokasi dan gunakan wilayah yang direkomendasikan\*
        - **Sambungkan Layanan Azure AI atau Azure OpenAI**: (Baru) *Isi otomatis dengan nama hub yang Anda pilih*
        - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum di pembantu lokasi mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari selengkapnya tentang [Menyempurnakan wilayah model](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Tinjau konfigurasi Anda dan buat proyek Anda.
1. Tunggu proyek Anda dibuat.

## Menyempurnakan model GPT-4

Karena menyempurnakan model membutuhkan waktu untuk diselesaikan, Anda akan memulai pekerjaan penyempurnaan sekarang dan kembali lagi setelah menjelajahi model dasar yang belum disempurnakan untuk diperbandingkan.

1. Unduh [himpunan data pelatihan](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) di `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`dan simpan sebagai file JSONL secara lokal.

    > **Catatan**: Perangkat Anda mungkin secara default berbalik menyimpan file sebagai file .txt. Pilih semua file dan hapus akhiran .txt untuk memastikan Anda menyimpan file sebagai JSONL.

1. Navigasikan ke halaman **Penyempurnaan** di bawah bagian **Membangun dan menyesuaikan** , menggunakan menu di sebelah kiri.
1. Pilih tombol untuk menambahkan model penyempurnaan baru, pilih `gpt-4` model, pilih **Selanjutnya** lalu **Konfirmasi**.
1. **Sempurnakan** model menggunakan konfigurasi berikut:
    - **Versi model**: *Pilih versi default*
    - **Akhiran model**: `ft-travel`
    - **Sumber daya AI yang tersambung**: *Pilih koneksi yang telah dibuat saat Anda membuat hub. Harus dipilih secara default.*.
    - **Data pelatihan**: Mengunggah file

    <details>  
    <summary><b>Tips pemecahan masalah</b>: Kesalahan izin</summary>
    <p>Jika Anda menerima kesalahan izin saat membuat alur perintah baru, coba langkah berikut untuk memecahkan masalah:</p>
    <ul>
        <li>Di portal Azure, pilih Penjelajah Sumber Daya dari Semua Layanan.</li>
        <li>Pada tab Identitas di bawah Manajemen Sumber Daya, konfirmasikan bahwa itu adalah identitas terkelola yang ditetapkan sistem.</li>
        <li>Lakukan navigasi ke Akun Penyimpanan. Pada halaman IAM, tambahkan penetapan peran <em>Pemilik Data Blob Penyimpanan</em>.</li>
        <li>Di bawah <strong>Tetapkan akses ke</strong>, pilih <strong>Identitas Terkelola</strong>, <strong>+ Pilih anggota</strong>, dan pilih <strong>Semua identitas terkelola yang ditetapkan sistem</strong>, dan pilih sumber daya layanan Azure AI Anda.</li>
        <li>Tinjau dan tetapkan untuk menyimpan pengaturan baru dan coba lagi langkah sebelumnya.</li>
    </ul>
    </details>

    - **Unggah file**: Pilih file JSONL yang Anda unduh di langkah sebelumnya.
    - **Data validasi**: Tidak ada
    - **Parameter tugas**: *Pertahankan pengaturan default*
1. Penyempurnaan akan dimulai dan mungkin perlu waktu untuk menyelesaikannya.

> **Catatan**: Penyempurnaan dan penyebaran dapat memakan waktu (30 menit atau lebih), jadi Anda mungkin perlu memeriksa kembali secara berkala. Anda sudah dapat melanjutkan dengan langkah berikutnya saat menunggu.

## Mengobrol dengan model dasar

Saat Anda menunggu pekerjaan penyempurnaan selesai, mari kita mengobrol dengan model GPT 4 base untuk menilai performanya.

1. Navigasikan ke halaman **Model + titik akhir** di bawah bagian **Aset saya**, menggunakan menu di sebelah kiri.
1. Pilih tombol **+ Sebarkan model** , dan pilih opsi **Sebarkan model dasar**.
1. Sebarkan `gpt-4`model dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik untuk model Anda, Anda dapat menggunakan default*
    - **Tipe penyebaran**: Standar
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default

> **Catatan**: Jika lokasi sumber daya AI Anda saat ini tidak memiliki kuota yang tersedia untuk model yang ingin Anda terapkan, Anda akan diminta untuk memilih lokasi lain tempat sumber daya AI baru akan dibuat dan tersambung ke proyek Anda.

1. Saat penerapan telah siap, pilih tombol **Buka di playground**.
1. Verifikasi bahwa `gpt-4`model dasar yang Anda terapkan dipilih di panel penyiapan.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan lihat responnya.
    Jawabannya sangat umum. Ingatlah bahwa kita ingin membuat aplikasi obrolan yang menginspirasi orang untuk bepergian.
1. Perbarui pesan sistem di panel pengaturan dengan perintah berikut:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Pilih **Terapkan Perubahan**, lalu pilih **Hapus obrolan**, dan tanyakan lagi `What can you do?` Sebagai respons, asisten dapat memberi tahu Anda bahwa itu dapat membantu Anda memesan penerbangan, hotel, dan mobil sewaan untuk perjalanan Anda. Anda ingin menghindari perilaku ini.
1. Perbarui pesan sistem lagi dengan perintah baru:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Pilih **Terapkan perubahan**, dan **Hapus obrolan**.
1. Lanjutkan pengujian aplikasi obrolan Anda untuk memverifikasi bahwa aplikasi tersebut tidak memberikan informasi apa pun yang tidak didasarkan pada data yang diambil. Misalnya, ajukan pertanyaan berikut dan tinjau jawaban model, berikan perhatian khusus pada nada dan gaya penulisan yang digunakan model untuk merespons:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Meninjau file pelatihan

Model dasar tampaknya bekerja cukup baik, tetapi Anda mungkin mencari gaya percakapan tertentu dari aplikasi AI generatif Anda. Data pelatihan yang digunakan untuk penyempurnaan memberi Anda kesempatan untuk membuat contoh eksplisit dari jenis respons yang Anda inginkan.

1. Buka file JSONL yang Anda unduh sebelumnya (Anda dapat membukanya di editor teks apa pun)
1. Periksa daftar dokumen JSON dalam file data pelatihan. Yang pertama harus mirip dengan ini (diformat agar mudah dibaca):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Setiap contoh interaksi dalam daftar menyertakan pesan sistem yang sama dengan yang Anda uji dengan model dasar, perintah pengguna yang terkait dengan kueri perjalanan, dan respons. Gaya respons dalam data pelatihan akan membantu model yang disempurnakan mempelajari cara responsnya.

## Menyebarkan model yang disempurnakan

Saat penyempurnaan telah berhasil diselesaikan, Anda dapat menyebarkan model yang disempurnakan tersebut.

1. Navigasi ke halaman **Penyempurnaan** di bawah **Membangun dan menyesuaikan** untuk menemukan pekerjaan penyempurnaan Anda dan statusnya. Jika masih berjalan, Anda dapat memilih untuk terus mengobrol dengan model dasar yang disebarkan atau beristirahat. Jika selesai, Anda dapat melanjutkan.
1. Pilih model yang disempurnakan. Pilih tab **Metrik** dan jelajahi metrik penyempurnaan.
1. Sebarkan model yang disempurnakan dengan konfigurasi berikut:
    - **Nama penyebaran**: *Nama unik untuk model Anda, Anda dapat menggunakan default*
    - **Tipe penyebaran**: Standar
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default
1. Tunggu hingga penyebaran selesai sebelum Anda dapat mengujinya, ini mungkin memakan waktu cukup lama. Periksa **Status penyediaan** hingga berhasil (Anda mungkin perlu menyegarkan browser untuk melihat status yang telah diperbarui).

## Menguji model yang disempurnakan

Sekarang, setelah menyebarkan model yang disempurnakan, Anda dapat menguji model tersebut seperti Anda menguji model dasar Anda yang telah disebarkan.

1. Saat penyebaran siap, navigasikan ke model yang disempurnakan dan pilih **Buka di taman bermain**.
1. Pastikan pesan sistem menyertakan instruksi berikut:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Uji model yang disempurnakan Anda untuk menilai apakah perilakunya lebih konsisten sekarang. Misalnya, ajukan pertanyaan berikut lagi dan jelajahi jawaban model:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Setelah meninjau respons, bagaimana hasilnya dibandingkan dengan model dasar?

## Penghapusan

Jika Anda telah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
