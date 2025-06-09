---
lab:
  title: Membuat aplikasi AI generatif yang menggunakan data Anda sendiri
  description: Pelajari cara menggunakan model Retrieval Augmented Generation (RAG) untuk membuat aplikasi obrolan yang mendasarkan perintah dengan menggunakan data Anda sendiri.
---

# Membuat aplikasi AI generatif yang menggunakan data Anda sendiri

Retrieval Augmented Generation (RAG) adalah teknik yang digunakan untuk membangun aplikasi yang mengintegrasikan data dari sumber data kustom ke dalam permintaan untuk model AI generatif. RAG adalah pola yang umum digunakan untuk mengembangkan aplikasi AI generatif - aplikasi berbasis obrolan yang menggunakan model bahasa untuk menafsirkan input dan menghasilkan respons yang sesuai.

Dalam latihan ini, Anda akan menggunakan Azure AI Foundry untuk mengintegrasikan data khusus ke dalam solusi AI generatif.

Latihan ini memakan waktu sekitar **45** menit.

> **Catatan**: Latihan ini didasarkan pada layanan pra-rilis, yang dapat berubah sewaktu-waktu.

## Buat sumber daya Azure AI Foundry

Mari kita mulai dengan membuat proyek sumber daya Azure AI Foundry.

1. Di browser web, buka [portal Azure](https://portal.azure.com) di `https://portal.azure` dan masuk menggunakan kredensial Azure Anda. Tutup tips atau panel mulai cepat yang dibuka saat pertama kali Anda masuk.
1. Buat sumber daya baru`Azure AI Foundry` dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Nama**: *Nama yang valid untuk sumber daya Azure AI Foundry Anda*
    - **Wilayah**: Pilih dari salah satu wilayah berikut
        - US Timur 2
        - Swedia Tengah
    - **Nama proyek bawaan**: *Nama yang valid untuk proyek Anda*

1. Tunggu hingga sumber daya selesai dibuat, lalu buka halamannya di portal Azure.
1. Di halaman sumber daya Azure AI Foundry Anda, pilih **Buka portal Azure AI Foundryl** 

## Terapkan model

Anda memerlukan dua model untuk mengimplementasikan solusi Anda:

- Model *penyematan* untuk memvektorisasi data teks untuk pengindeksan dan pemrosesan yang efisien.
- Model yang dapat menghasilkan respons bahasa alami terhadap pertanyaan berdasarkan data Anda.

1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Buat penyebaran baru model **text-embedding-ada-002** dengan pengaturan berikut dengan memilih **Sesuaikan** di wizard Penerapan model:

    - **Nama penyebaran**: *Nama yang valid untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar Global
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI terhubung**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Rate Token per Menit (ribuan)**: 50K *(atau jumlah maksimum yang tersedia dalam langganan Anda jika kurang dari 50K)*
    - **Filter konten**: DefaultV2

    > **Catatan**: Jika lokasi sumber daya AI Anda saat ini tidak memiliki kuota yang tersedia untuk model yang ingin Anda terapkan, Anda akan diminta untuk memilih lokasi lain tempat sumber daya AI baru akan dibuat dan tersambung ke proyek Anda.

1. Kembali ke halaman **Models + titik akhir** dan ulangi langkah-langkah sebelumnya untuk menyebarkan model **gpt-4o** menggunakan penyebaran **Standar Global** dari versi terbaru dengan batas rate TPM **50K** (atau batas maksimum yang tersedia dalam langganan Anda jika kurang dari 50K).

    > **Catatan**: Mengurangi Token Per Menit (TPM) membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 50.000 TPM cukup untuk data yang digunakan dalam latihan ini.

## Menambahkan data ke proyek Anda

Data untuk aplikasi Anda terdiri dari serangkaian brosur perjalanan dalam format PDF dari agen perjalanan fiktif *Margie's Travel*. Mari kita tambahkan ke proyek.

1. Di tab browser baru, unduh [arsip zip brosur](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) dari `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` dan ekstrak ke folder bernama **brosur** pada sistem file lokal Anda.
1. Di portal Azure AI Foundry, proyek Anda pada panel navigasi di sebelah kiri, pilih **Taman Bermain**, lalu pilih **Coba Obrolan taman bermain**.
1. Di panel **Penyiapan** taman bermain, perluas bagian **Tambahkan bagian data Anda** dan pilih **Tambahkan sumber**.
1. Di wizard **Tambahkan data** Anda, perluas menu drop-down untuk memilih **Unggah file/folder**.
1. Kemudian buat sumber daya Layanan Azure AI baru dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya yang sama dengan sumber daya Pencarian Azure AI Anda*
    - **Nama akun penyimpanan:**: *Nama yang valid untuk sumber daya akun penyimpanan Anda*
    - **Wilayah**: *Wilayah yang sama dengan sumber daya Azure AI Foundry Anda*
    - **Performa**: Standar
    - **Redundansi**: LRS
1. Buat sumber daya Anda dan tunggu hingga penyebaran selesai.
1. Kembali ke tab Azure AI Foundry Anda, refresh daftar sumber daya penyimpanan Azure Blob dan pilih akun yang baru dibuat.

    > **Catatan**: Jika Anda menerima peringatan bahwa Azure OpenAI perlu izin Anda untuk mengakses sumber daya Anda, pilih **Aktifkan CORS**.

1. Buat sumber daya Pencarian Azure AI baru dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya yang sama dengan sumber daya Pencarian Azure AI Anda*
    - **Nama layanan**: *Nama yang valid untuk sumber daya Azure AI Pencarian*
    - **Wilayah**: *Wilayah yang sama dengan sumber daya Azure AI Foundry Anda*
    - **Tingkat harga**: Dasar

1. Buat sumber daya Anda dan tunggu hingga penyebaran selesai.
1. Kembali ke tab Azure AI Foundry Anda, refresh daftar sumber daya Azure AI Pencarian dan pilih akun yang baru dibuat.
1. Beri nama indeks Anda.`brochures-index`.
1. Aktifkan opsi **Tambahkan pencarian vektor ke sumber daya pencarian ini** dan pilih model penyematan yang Anda sebarkan sebelumnya. Pilih **Selanjutnya**.

   >**Catatan**: Mungkin perlu waktu beberapa saat hingga **wizard Tambahkan data** mengenali model penyematan yang Anda sebarkan, jadi jika opsi pencarian vektor tidak dapat diakftifkan, batalkan wizard, tunggu beberapa menit dan coba lagi.

1. Unggah semua file .pdf dari **folder brosur** yang Anda ekstrak sebelumnya lalu pilih **Berikutnya**.
1. **Dalam langkah Manajemen data**, pilih jenis pencarian **Hibrid (vektor + kata kunci)** dan ukuran gugus **1024**. Pilih **Selanjutnya**.
1. Di langkah **Koneksi data**, pilih **kunci API**sebagai jenis autentikasi Pilih **Selanjutnya**.
1. Tinjau semua langkah konfigurasi lalu pilih **Simpan dan tutup**.
1. Tunggu hingga proses pengindeksan selesai, yang dapat memakan waktu cukup lama tergantung pada sumber daya komputasi yang tersedia dalam langganan Anda.

    > **Tips**: Saat Anda menunggu indeks dibuat, mengapa tidak melihat brosur yang Anda unduh untuk membiasakan diri dengan kontennya?

## Menguji indeks di taman bermain

Sebelum menggunakan indeks Anda dalam alur prompt berbasis RAG, mari kita verifikasi bahwa indeks tersebut dapat digunakan untuk memengaruhi respons AI generatif.

1. Pada halaman Orolan taman bermain, di panel Penyiapan, pastikan Anda memilih penyebaran model **gpt-4**. Kemudian, di panel sesi obrolan utama, kirimkan perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus didasarkan pada data dalam indeks.

## Membuat aplikasi klien RAG

Sekarang setelah Anda memiliki indeks yang berfungsi, Anda dapat menggunakan Azure OpenAI SDK untuk menerapkan pola RAG dalam aplikasi klien. Mari kita jelajahi kode untuk mencapainya dalam contoh sederhana.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Kembali ke tab browser yang berisi portal Azure (biarkan portal Azure AI Foundry tetap terbuka di tab yang ada).
1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell*** dengan tidak ada penyimpanan pada langganan Anda.

    Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure. Anda dapat mengubah ukuran atau memaksimalkan panel ini untuk mempermudah pekerjaan.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    **<font color="red">Pastikan Anda telah beralih ke versi klasik cloud shell sebelum melanjutkan.</font>**

1. Di panel cloud shell, masukkan perintah berikut untuk mengkloning repo GitHub yang berisi file kode untuk latihan ini (ketik perintah, atau salin ke clipboard lalu klik kanan di baris perintah dan tempel sebagai teks biasa):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tips**: Saat Anda menempelkan perintah ke cloudshell, ouputnya mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

1. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode aplikasi obrolan:

    > **Catatan**: Ikuti langkah-langkah untuk bahasa pemrograman yang Anda pilih.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka OpenAI SDK:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.OpenAI
    ```
    

1. Masukkan perintah berikut untuk mengedit file konfigurasi yang telah disediakan:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    File dibuka dalam editor kode.

1. Dalam file kode, ganti tempat penampung berikut: 
    - **your_openai_endpoint**: Titik akhir Open AI dari halaman **Overview** proyek Anda di portal Azure AI Foundry (pastikan untuk memilih tab kemampuan **Azure OpenAI**, bukan kemampuan Azure AI Inference atau Azure AI Services).
    - **your_openai_api_key** Kunci API Open AI dari halaman **Overview** proyek Anda di portal Azure AI Foundry (pastikan untuk memilih tab kemampuan **Azure OpenAI**, bukan kemampuan Azure AI Inference atau Azure AI Services).
    - **your_chat_model**: Nama yang Anda tetapkan untuk penyebaran model **gpt-4o** Anda, dari halaman **Models + endpoints** di portal Azure AI Foundry (nama default-nya adalah `gpt-4o`).
    - **your_embedding_model**: Nama yang Anda tetapkan untuk penerapan model **text-embedding-ada-002** Anda, dari halaman **Models + endpoints** di portal Azure AI Foundry (nama default-nya adalah `text-embedding-ada-002`).
    - **** your_search_endpoint: URL untuk sumber daya Azure AI Search Anda. Anda akan menemukannya di **Management center** di portal Azure AI Foundry.
    - **your_search_api_key**: Kunci API untuk sumber daya Azure AI Search Anda. Anda akan menemukannya di **Management center** di portal Azure AI Foundry.
    - **your_index**: Ganti dengan nama indeks Anda dari halaman **Data + indexes** untuk proyek Anda di portal Azure AI Foundry (seharusnya `brochures-index`).
1. Setelah Anda mengganti tempat penampung, di editor kode,gunakan perintah **CTRL+S** atau **Klik kanan > Simpan** untuk menyimpan perubahan Anda dan kemudian gunakan perintah **CTRL+Q** atau **Klik kanan > Keluar** untuk menutup editor kode sambil tetap membuka baris perintah cloud shell.

### Menjelajahi kode untuk mengimplementasikan pola RAG

1. Masukkan perintah berikut untuk mengedit file kode yang telah disediakan:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Tinjau kode dalam file, dengan mencatat bahwa kode tersebut:
    - Membuat klien Azure OpenAI menggunakan titik akhir, kunci, dan model obrolan.
    - Membuat pesan sistem yang sesuai untuk solusi obrolan terkait perjalanan.
    - Mengirimkan perintah (termasuk pesan sistem dan pesan pengguna berdasarkan masukan pengguna) ke klien Azure OpenAI, dengan menambahkan:
        - Detail koneksi untuk indeks Pencarian Azure AI yang akan ditanyakan.
        - Detail model penyematan yang akan digunakan untuk membuat vektor pertanyaan.\*.
    - Menampilkan respons dari perintah yang didasarkan.
    - Menambahkan respons ke riwayat obrolan.

    \**Pertanyaan untuk indeks pencarian didasarkan pada perintah, dan digunakan untuk menemukan teks yang relevan dalam dokumen yang telah diindeks. Anda dapat menggunakan pencarian berbasis kata kunci yang mengirimkan pertanyaan sebagai teks, tetapi pencarian berbasis vektor bisa lebih efisien — oleh karena itu digunakan model penyematan untuk membuat vektor teks pertanyaan sebelum dikirimkan.*

1. Gunakan perintah **CTRL+Q** untuk menutup editor kode tanpa menyimpan perubahan apa pun, sambil tetap membuka baris perintah cloud shell.

### Jalankan aplikasi obrolan tersebut

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan aplikasinya:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Saat diminta, masukkan pertanyaan, seperti `Where should I go on vacation to see architecture?` dan tinjau respons dari model AI generatif Anda.

    Perhatikan bahwa respons menyertakan referensi sumber untuk menunjukkan data terindeks tempat jawaban ditemukan.

1. Coba pertanyaan tindak lanjut, misalnya `Where can I stay there?`

1. Setelah selesai, tekan enter `quit` untuk mengakhiri program. Lalu tutup panel Cloud Shell.

## Penghapusan

Untuk menghindari biaya Azure dan pemanfaatan sumber daya yang tidak perlu, Anda harus menghapus sumber daya yang Anda sebarkan dalam latihan ini.

1. Jika Anda telah selesai menjelajahi Azure AI Foundry, kembali ke [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda jika perlu. Kemudian hapus sumber daya di grup sumber daya tempat Anda memprovisikan sumber daya Azure AI Search dan Azure AI Anda.
