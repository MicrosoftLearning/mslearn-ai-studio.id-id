---
lab:
  title: Mengevaluasi performa model AI generatif
  description: Pelajari cara mengevaluasi model dan perintah obrolan untuk mengoptimalkan kinerja aplikasi obrolan Anda dan kemampuannya dalam merespons dengan tepat.
---

# Mengevaluasi performa model AI generatif

Dalam latihan ini, Anda akan menggunakan evaluasi manual dan otomatis untuk menilai performa model di portal Azure AI Foundry.

Latihan ini akan memakan waktu sekitar **30** menit.

> **Catatan**: Beberapa teknologi yang digunakan dalam latihan ini sedang dalam pratinjau atau dalam pengembangan aktif. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan yang tidak terduga.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke laman beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama yang valid untuk proyek Anda dan jika hub yang telah ada disarankan, pilih opsi untuk membuat yang baru. Kemudian tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung hub dan proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub** : *Nama yang valid untuk hub Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: Pilih salah satu wilayah berikut\*:
        - AS Timur 2
        - Prancis Tengah
        - UK Selatan
        - Swedia Tengah
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Pada saat penulisan, wilayah-wilayah ini mendukung evaluasi metrik keamanan AI. Ketersediaan model dibatasi oleh kuota regional. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

## Terapkan model

Dalam latihan ini, Anda akan mengevaluasi performa model gpt-4o-mini. Anda juga akan menggunakan model gpt-4o untuk menghasilkan metrik evaluasi yang dibantu AI.

1. Di panel navigasi di sebelah kiri untuk proyek Anda, di bagian **My assets**, pilih halaman **Models + endpoints**.
1. Pada halaman **Model + titik akhir** , di tab **Penyebaran model** di menu **+ Sebarkan model** pilih **Sebarkan model dasar**.
1. Cari model **gpt-4o** dari daftar, pilih dan konfirmasi.
1. Terapkan model dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penyeberan:
    - **Nama penyebaran**: *Nama yang valid untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar Global
    - **Pembaruan versi otomatis**: Diaktifkan
    - **Versi model**: *Pilih versi terbaru yang tersedia*
    - **Sumber daya AI yang terhubung**: *Pilih koneksi sumber daya Azure OpenAI Anda*
    - **Batas Rate Token per Menit (ribuan)**: 50K *(atau jumlah maksimum yang tersedia dalam langganan Anda jika kurang dari 50K)*
    - **Filter konten**: DefaultV2

    > **Catatan**: Mengurangi TPM membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 50.000 TPM seharusnya cukup untuk data yang digunakan dalam latihan ini. Jika kuota yang tersedia lebih rendah dari ini, Anda akan dapat menyelesaikan latihan tetapi Anda mungkin mengalami kesalahan jika batas rate terlampaui.

1. Tunggu hingga penerapan selesai.
1. Kembali ke halaman **Models + endpoints** dan ulangi langkah-langkah sebelumnya untuk menyebarkan model **gpt-4o-mini** dengan pengaturan yang sama.

## Mengevaluasi model secara manual

Anda dapat meninjau respons model secara manual berdasarkan data pengujian. Peninjauan secara manual memungkinkan Anda menguji input yang berbeda untuk mengevaluasi apakah model berfungsi seperti yang diharapkan.

1. Di tab browser baru, unduh [travel_evaluation_data.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl) dari`https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl` dan simpan di folder lokal sebagai **travel_evaluation_data.jsonl** (pastikan untuk menyimpannya sebagai file .jsonl, bukan file .txt).
1. Kembali ke tab portal Azure AI Foundry, di panel navigasi, di bagian **Nilai dan tingkatkan** , pilih **Evaluasi**.
1. Di halaman **Evaluasi** , lihat tab **Evaluasi manual** dan pilih **+ New manual evaluation**.
1. Di bagian **Konfigurasi**, dalam daftar **Model**, pilih penyebaran model **gpt-4o-mini** Anda.
1. Ubah **Pesan Sistem** menjadi instruksi berikut untuk asisten perjalanan AI:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Di bagian **Hasil evaluasi manual**, pilih **Impor data tes** dan unggah file **travel_evaluation_data.jsonl** yang Anda unduh sebelumnya; buat pemetaan bidang himpunan data sebagai berikut:
    - **Input**: Pertanyaan
    - **Respon yang diharapkan**: ExpectedResponse
1. Tinjau pertanyaan dan jawaban yang diharapkan dalam file pengujian - Anda akan menggunakannya untuk mengevaluasi respons yang dihasilkan model.
1. Pilih **Jalankan** dari bilah atas untuk menghasilkan output untuk semua pertanyaan yang Anda tambahkan sebagai input. Setelah beberapa menit, respons dari model harus ditampilkan di kolom **Output** baru, seperti ini:

    ![Tangkapan layar halaman evaluasi manual di portal Azure AI Foundry.](./media/manual-evaluation.png)

1. Tinjau output untuk setiap pertanyaan, membandingkan output dari model dengan jawaban yang diharapkan lalu "beri skor" pada hasilnya dengan memilih ikon jempol ke atas atau jempol ke bawah di kanan bawah setiap respons.
1. Setelah Anda menilai responsnya, tinjau petak ringkasan di atas daftar. Kemudian di toolbar, pilih **Simpan hasil** dan tetapkan nama yang sesuai. Menyimpan hasil memungkinkan Anda untuk mengambilnya nanti untuk evaluasi atau perbandingan lebih lanjut dengan model yang berbeda.

## Gunakan evaluasi otomatis

Meskipun membandingkan output model secara manual dengan respons yang Anda harapkan dapat menjadi cara yang berguna untuk menilai performa model, ini adalah pendekatan yang memakan waktu dalam skenario di mana Anda mengharapkan berbagai pertanyaan dan respons; dan pendekatan tersebut menyediakan sedikit cara metrik standar yang dapat Anda gunakan untuk membandingkan berbagai model dan kombinasi perintah.

Evaluasi otomatis adalah pendekatan yang mencoba mengatasi kekurangan ini dengan menghitung metrik dan menggunakan AI untuk menilai respons berdasarkan koherensi, relevansi, dan faktor lainnya.

1. Gunakan panah belakang (**&larr;**) di **samping judul halaman Evaluasi manual** untuk kembali ke halaman **Evaluasi**.
1. Lihat tab **Evaluasi otomatis**.
1. Pilih **Create a new evaluation**, dan saat diminta, pilih opsi untuk mengevaluasi **Model dan perintah**
1. Di halaman **Buat evaluasi baru**, di bagian **Informasi dasar**, tinjau nama evaluasi default yang dibuat secara otomatis (Anda dapat mengubahnya jika mau) dan memilih model penyebaran **gpt-40-mini** Anda.
1. Ubah **Pesan Sistem** ke instruksi yang sama untuk asisten perjalanan AI yang Anda gunakan sebelumnya:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Di bagian **Konfigurasikan data pengujian**, perhatikan bahwa Anda dapat menggunakan model GPT untuk membuat data pengujian bagi Anda (yang kemudian dapat Anda edit dan tambahkan agar sesuai dengan harapan Anda), menggunakan himpunan data yang ada, atau mengunggah file. Dalam latihan ini, pilih **Use existing dataset** lalu pilih kumpulan data **travel_evaluation_data_jsonl_*xxxx...*** (yang dibuat saat Anda mengunggah file .jsonl sebelumnya).
1. Tinjau baris sampel dari himpunan data, lalu di bagian **Choose your data column**, pilih pemetaan kolom berikut:
    - **Kueri**: Pertanyaan
    - **Konteks**: *Biarkan ini kosong. Ini digunakan untuk mengevaluasi "groundedness" saat mengaitkan sumber data kontekstual dengan model Anda.*
    - **Kebenaran dasar**: ExpectedAnswer
1. Di bagian **Pilih apa yang ingin Anda evaluasi** , pilih <u>semua</u> dari kategori evaluasi berikut:
    - Kualitas AI (bantuan AI)
    - Risiko dan keamanan (bantuan AI)
    - Kualitas AI (NLP)
1. Dalam daftar **Choose a model deployment as judge**, pilih model**gpt-4o** Anda. Model ini akan digunakan untuk menilai respons dari model **gpt-4o-mini** untuk kualitas terkait bahasa dan metrik perbandingan AI generatif standar.
1. Pilih **Create** untuk memulai proses evaluasi, dan tunggu hingga selesai. Proses ini memerlukan waktu beberapa menit.

    > **Tips**: Jika muncul kesalahan yang menunjukkan bahwa izin proyek sedang ditetapkan, tunggu sebentar lalu pilih **Create** lagi. Diperlukan beberapa waktu agar izin sumber daya untuk proyek yang baru dibuat disebarkan.

1. Setelah evaluasi selesai, gulir ke bawah jika perlu untuk melihat area **Dasbor metrik** kemudian lihat metrik **Kualitas AI (Bantuan AI)**:

    ![Tangkapan layar metrik kualitas AI di portal Azure AI Foundry.](./media/ai-quality-metrics.png)

    Gunakan ikon **<sup>(i) </sup>** untuk melihat definisi metrik.

1. Lihat tab **Risiko dan keamanan** untuk melihat metrik yang terkait dengan konten yang berpotensi membahayakan.
1. Lihat tab **kualitas AI (NLP**) untuk melihat metrik standar untuk model AI generatif.
1. Gulir kembali ke bagian atas halaman jika perlu, lalu pilih tab **Data** untuk melihat data mentah dari evaluasi. Data mencakup metrik untuk setiap input serta penjelasan tentang alasan yang diterapkan model gpt-4o saat menilai respons.

    ![Tangkapan layar data evaluasi di portal Azure AI Foundry.](./media/evaluation-data.png)

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
