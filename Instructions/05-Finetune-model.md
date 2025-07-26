---
lab:
  title: Menyempurnakan model bahasa
  description: Pelajari cara menggunakan data pelatihan Anda sendiri untuk menyempurnakan model dan menyesuaikan perilakunya.
---

# Menyempurnakan model bahasa

Saat Anda ingin model bahasa berperilaku dengan gaya tertentu, Anda dapat menggunakan rekayasa perintah untuk menentukan perilaku yang diinginkan. Ketika Anda ingin meningkatkan konsistensi atas perilaku yang diinginkan, Anda dapat memilih untuk menyempurnakan model, membandingkannya dengan pendekatan rekayasa prompt Anda untuk mengevaluasi metode mana yang paling sesuai dengan kebutuhan Anda.

Dalam latihan ini, Anda akan menyempurnakan model bahasa dengan Azure AI Foundry yang ingin Anda gunakan untuk skenario aplikasi percakapan yang disesuaikan. Anda akan membandingkan model yang disempurnakan dengan model dasar untuk menilai apakah model yang disempurnakan lebih sesuai dengan kebutuhan Anda.

Bayangkan Anda bekerja untuk agen perjalanan dan Anda mengembangkan aplikasi obrolan untuk membantu orang merencanakan liburan mereka. Targetnya adalah untuk membuat obrolan sederhana dan inspiratif yang menyarankan destinasi dan aktivitas dengan nada percakapan yang konsisten dan ramah.

Latihan ini akan memakan waktu sekitar **60** menit\*.

> \***Catatan**: Waktu ini adalah perkiraan berdasarkan pengalaman rata-rata. Penyempurnaan tergantung pada sumber daya infrastruktur cloud, yang dapat memakan waktu yang bervariasi untuk disediakan tergantung pada kapasitas pusat data dan permintaan bersamaan. Beberapa aktivitas dalam latihan ini mungkin membutuhkan waktu yang <u>lama</u> untuk diselesaikan, dan memerlukan kesabaran. Jika ada yang memakan waktu cukup lama, pertimbangkan untuk meninjau [Dokumentasi penyempurnaan Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) atau beristirahat. Ada kemungkinan sebagian proses kehabisan waktu atau tampak berjalan tanpa batas waktu. Sebagian teknologi yang digunakan dalam latihan ini dalam pratinjau atau dalam pengembangan aktif. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan tak terduga.

## Menyebarkan model dalam proyek Azure AI Foundry

Mari mulai dengan menyebarkan model dalam proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pada bagian **Jelajahi model dan kemampuan** , cari`gpt-4o` model; yang akan kita gunakan dalam proyek kita.
1. Dalam hasil pencarian, pilih **model gpt-4o** untuk melihat detailnya, lalu di bagian atas halaman untuk model, pilih **Gunakan model** ini.
1. Saat diminta untuk membuat proyek, masukkan nama yang valid untuk proyek Anda dan perluas **opsi** Tingkat Lanjut.
1. Pilih **Sesuaikan** dan tentukan pengaturan berikut untuk proyek Anda:
    - **Sumber daya Azure AI Foundry**: *Nama yang valid untuk sumber daya Azure AI Foundry Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: *Pilih salah satu wilayah berikut:*\*
        - US Timur 2
        - AS Tengah Bagian Utara
        - Swedia Tengah

    > \* Pada saat penulisan, berbagai wilayah ini mendukung penyempurnaan untuk model gpt-4o.

1. Pilih **Create**, lalu tunggu proyek Anda dibuat. Jika diminta, sebarkan model gpt-4o menggunakan jenis penyebaran **Standar global** dan sesuaikan detail penyebaran untuk mengatur **batas tarif Token per menit** 50K (atau maksimum yang tersedia jika kurang dari 50K).

    > **Catatan**: Mengurangi TPM membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 50.000 TPM seharusnya cukup untuk data yang digunakan dalam latihan ini. Jika kuota yang tersedia lebih rendah dari ini, Anda akan dapat menyelesaikan latihan tetapi Anda mungkin mengalami kesalahan jika batas rate terlampaui.

1. Saat proyek Anda dibuat, ruang obrolan akan terbuka secara otomatis sehingga Anda dapat menguji model Anda:
1. Di panel **Penyiapan** , perhatikan nama penyebaran model Anda; yang seharusnya **gpt-4o**. Anda dapat mengonfirmasi ini dengan melihat penyebaran di **halaman Model dan titik** akhir (cukup buka halaman tersebut di panel navigasi di sebelah kiri).
1. Di panel navigasi di sebelah kiri, pilih **Gambaran Umum** untuk melihat halaman utama untuk proyek Anda; yang terlihat seperti ini:

    ![Tangkapan layar halaman gambaran umum proyek Azure AI Foundry .](./media/ai-foundry-project.png)

## Menyempurnakan model.

Karena menyesuaikan model butuh waktu untuk selesai, Anda akan memulai pekerjaan penyesuaian sekarang dan kembali lagi setelah menjelajahi model dasar base gpt-4o yang sudah dijalankan.

1. Unduh [himpunan data pelatihan](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) di `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`dan simpan sebagai file JSONL secara lokal.

    > **Catatan**: Perangkat Anda mungkin secara default berbalik menyimpan file sebagai file .txt. Pilih semua file dan hapus akhiran .txt untuk memastikan Anda menyimpan file sebagai JSONL.

1. Navigasikan ke halaman **Penyempurnaan** di bawah bagian **Membangun dan menyesuaikan** , menggunakan menu di sebelah kiri.
1. Pilih tombol untuk menambahkan model fine-tune, pilih model **gpt-4o**, lalu pilih **Berikutnya**.
1. **Sempurnakan** model menggunakan konfigurasi berikut:
    - **Metode penyesuaian**: yang Diawasi
    - **Model** dasar: *Pilih versi **default gpt-4o***
    - **Data** pelatihan: *Pilih opsi untuk **Menambahkan data** pelatihan dan unggah dan terapkan file .jsonl yang Anda unduh sebelumnya*
    - **Akhiran model**: `ft-travel`
    - **random([seed])**
1. Kirim detail penyempurnaan, dan pekerjaan akan dimulai. Proses ini mungkin perlu waktu cukup lama untuk selesai. Anda dapat melanjutkan dengan bagian latihan berikutnya saat menunggu.

> **Catatan**: Penyempurnaan dan penyebaran dapat memakan waktu (30 menit atau lebih), jadi Anda mungkin perlu memeriksa kembali secara berkala. Anda dapat melihat detail selengkapnya tentang kemajuan sejauh ini dengan memilih pekerjaan model penyesuaian dan melihat tab **Log**-nya.

## Mengobrol dengan model dasar

Saat Anda menunggu pekerjaan penyesuaian selesai, mari mengobrol dengan model GPT 4o dasar untuk menilai performanya.

1. Pada panel navigasi di sebelah kiri,pilih **Ruang obrolan**dan buka **Ruang obrolan**.
1. Verifikasi model dasar **gpt-4o**yang dijalankan dipilih dalam panel penyiapan.
1. Di jendela obrolan, masukkan kueri `What can you do?` dan lihat responnya.

    Jawabannya mungkin cukup umum. Ingatlah bahwa kita ingin membuat aplikasi obrolan yang menginspirasi orang untuk bepergian.

1. Perbarui pesan sistem di panel pengaturan dengan perintah berikut:

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Pilih**Terapkan perubahan**untuk memperbarui pesan sistem.
1. Di jendela obrolan, masukkan pertanyaan `What can you do?` dan lihat responnya.
1 Sebagai respons, asisten dapat memberi tahu Anda bahwa itu dapat membantu Anda memesan penerbangan, hotel, dan mobil sewaan untuk perjalanan Anda. Anda ingin menghindari perilaku ini.

1. Perbarui pesan sistem lagi dengan perintah baru:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
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

    > **Tips**: Gunakan tombol **Refresh** di halaman penyesuaian untuk me-refresh tampilan. Jika pekerjaan penyesuaian hilang sepenuhnya, refresh halaman di browser.

1. Pilih tautan pekerjaan penyesuaian untuk membuka halaman detailnya. Pilih tab **Metrik** dan jelajahi metrik penyesuaian.
1. Sebarkan model yang disempurnakan dengan konfigurasi berikut:
    - **Nama penyebaran**: *Nama yang valid untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Batas Rate Token per Menit (ribuan)**: 50K *(atau jumlah maksimum yang tersedia dalam langganan Anda jika kurang dari 50K)*
    - **Filter konten**: Default
1. Tunggu hingga penyebaran selesai sebelum Anda dapat mengujinya, ini mungkin memakan waktu cukup lama. Periksa **Status penyediaan** hingga berhasil (Anda mungkin perlu menyegarkan browser untuk melihat status yang telah diperbarui).

## Menguji model yang disempurnakan

Sekarang, setelah menyebarkan model yang disempurnakan, Anda dapat menguji model tersebut seperti Anda menguji model dasar Anda yang telah disebarkan.

1. Saat penyebaran siap, navigasikan ke model yang disempurnakan dan pilih **Buka di taman bermain**.
1. Pastikan pesan sistem menyertakan instruksi berikut:

    ```
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
