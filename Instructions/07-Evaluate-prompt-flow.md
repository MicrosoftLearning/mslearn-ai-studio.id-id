---
lab:
  title: Mengevaluasi performa model AI generatif
  description: Pelajari cara mengevaluasi model dan perintah obrolan untuk mengoptimalkan kinerja aplikasi obrolan Anda dan kemampuannya dalam merespons dengan tepat.
---

# Mengevaluasi performa model AI generatif

Dalam latihan ini, Anda akan menggunakan evaluasi manual dan otomatis untuk menilai performa model di portal Azure AI Foundry.

Latihan ini akan memakan waktu sekitar **30** menit.

> **Catatan**: Beberapa teknologi yang digunakan dalam latihan ini sedang dalam pratinjau atau dalam pengembangan aktif. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan yang tidak terduga.

## Membuat pusat penyimpanan AI dan proyek di Azure AI Foundry

Fitur Azure AI Foundry yang akan kita gunakan dalam latihan ini memerlukan proyek yang didasarkan pada sumber daya *hub* Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di browser, navigasikan ke `https://ai.azure.com/managementCenter/allResources` dan pilih **Buat baru**. Lalu pilih opsi untuk membuat **sumber daya hub AI** baru.
1. Di wizard **Buat proyek** masukkan nama yang valid untuk proyek Anda dan pilih opsi untuk membut hub baru. Kemudian gunakan tautan **Ganti nama hub** untuk menentukan nama yang valid untuk hub baru Anda, perluas **opsi Tingkat lanjut**, dan tentukan pengaturan berikut untuk proyek Anda:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Lokasi**: Pilih salah satu dari lokasi berikut ini (*Jika kemudian terjadi batas kuota terlampaui saat latihan Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.*):
        - AS Timur 2
        - Prancis Tengah
        - UK Selatan
        - Swedia Tengah

    > **Catatan**: Jika Anda bekerja dengan berlangganan Azure di mana kebijakan digunakan untuk membatasi nama sumber daya yang diizinkan, Anda mungkin perlu menggunakan tautan di bagian bawah kotak dialog **Buat proyek baru** untuk membuat hub menggunakan portal Azure.

    > **Tips**: Jika tombol **Buat** masih dinonaktifkan, pastikan untuk mengganti nama hub Anda menjadi nilai alfanumerik unik.

1. Tunggu proyek Anda dibuat.

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
1. Kembali ke halaman portal Azure AI Foundry, di panel navigasi, di bagian **Lindungi dan atur** , pilih **Evaluasi**.
1. Jika panel **Buat evaluasi** baru terbuka secara otomatis, pilih **Batal** untuk menutupnya.
1. Di halaman **Evaluasi** , lihat tab **Evaluasi manual** dan pilih **+ New manual evaluation**.
1. Di bagian **Konfigurasi**, dalam daftar **Model**, pilih penyebaran model **gpt-4o-mini** Anda.
1. Ubah **Pesan Sistem** menjadi instruksi berikut untuk asisten perjalanan AI:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Di bagian **Hasil evaluasi manual**, pilih **Impor data tes** dan unggah file **travel_evaluation_data.jsonl** yang Anda unduh sebelumnya; gulir ke bawah untuk pemetaan bidang himpunan data sebagai berikut:
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
1. Pilih **Buat evaluasi baru**dan ketika diperintahkan pilih opsi untuk mengevaluasi**Evaluasi model**dan pilihl**Berikutnya**.
1. Pada halaman **Pilih sumber** data, pilih **Gunakan himpunana data**Anda dan pilih **travel_evaluation_data_jsonl_*xxxx...*** himpunan data berdasarkan file yang Anda unggah sebelumnya, dan pilih **Berikutnya**.
1. Pada halaman **Uji model** Anda, pilih **model gpt-4o-mini** dan ubah **pesan** Sistem ke instruksi yang sama untuk asisten perjalanan AI yang Anda gunakan sebelumnya:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Pilih **bidang pertanyaan** pilih **\{\{item.pertanyaani\}\}**.
1. Pilih **Berikutnya** untuk pindah ke halaman berikutnya.
1. Pada halaman **Konfigurasi evaluator** , gunakan **tombol +Tambahkan** untuk menambahkan evaluator berikut, mengonfigurasi masing-masing sebagai berikut:
    - **Skor model**:
        - **Nama kriteria**: Kesamaan_semantik
        - **Nilai dengan**: *Pilih model gpt-4o** Anda ***
        - **Pengaturan pengguna** (di bagian bawah):


            Hasil: \{\{sample.output_text\}\}<br>
            Kebenaran Dasar: \{\{item. ExpectedResponse\}\}<br>
            <br>
        
    - **Evaluator skala likert**:
        - **Nama kriteria: Relevansi**: 
        - **Nilai dengan**: *Pilih model gpt-4o** Anda ***
        - **Pertanyaan**: \{\{item.question\}\}

    - **Kesamaan teks**:
        - **Nama kriteria**: F1_Score
        - **Kebenaran dasar**: \{\{item. ExpectedResponse\}\}

    - **Konten yang penuh kebencian dan tidak adil**:
        - **Nama kriteria**: Hate_and_unfairness
        - **Pertanyaan**: \{\{item.question\}\}

1. Pilih **Berikutnya** dan tinjau pengaturan evaluasi Anda. Anda seharusnya mengonfigurasi evaluasi untuk menggunakan himpunan data evaluasi perjalanan untuk mengevaluasi **model gpt-4o-mini**kesamaan semantik, relevansi, skor F1, dan bahasa yang penuh kebencian dan tidak adil.
1. Beri evaluasi nama yang sesuai, dan **Kirimkan** untuk memulai proses evaluasi,lalu tunggu hingga selesai. Proses ini memerlukan waktu beberapa menit. Anda dapat menggunakan tombol **bilah Refresh** untuk memeriksa status.

1. Setelah evaluasi selesai, jika perlu, gulir ke bawah untuk meninjau hasilnya.

    ![Tangkapan layar metrik evaluasi.](./media/ai-quality-metrics.png)

1. Di bagian atas halaman,pilih halaman **Data** untuk melihat data mentah dari evaluasi. Data mencakup metrik untuk setiap input serta penjelasan tentang alasan yang diterapkan model gpt-4o saat menilai respons.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
