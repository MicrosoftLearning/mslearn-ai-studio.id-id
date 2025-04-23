---
lab:
  title: Membuat aplikasi AI generatif yang menggunakan data Anda sendiri
  description: Pelajari cara menggunakan model Retrieval Augmented Generation (RAG) untuk membuat aplikasi obrolan yang mendasarkan perintah dengan menggunakan data Anda sendiri.
---

# Membuat aplikasi AI generatif yang menggunakan data Anda sendiri

Retrieval Augmented Generation (RAG) adalah teknik yang digunakan untuk membangun aplikasi yang mengintegrasikan data dari sumber data kustom ke dalam permintaan untuk model AI generatif. RAG adalah pola yang umum digunakan untuk mengembangkan aplikasi AI generatif - aplikasi berbasis obrolan yang menggunakan model bahasa untuk menafsirkan input dan menghasilkan respons yang sesuai.

Dalam latihan ini, Anda akan menggunakan portal Azure AI Foundry dan Azure AI Foundry serta Azure OpenAI SDK untuk mengintegrasikan data khusus ke dalam aplikasi AI generatif.

Latihan ini memakan waktu sekitar **45** menit.

> **Catatan**: Latihan ini didasarkan pada SDK pra-rilis, yang mungkin dapat berubah. Jika perlu, kami telah menggunakan versi paket tertentu; yang mungkin tidak mencerminkan versi terbaru yang tersedia. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan tak terduga.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry dan sumber daya layanan yang perlu didukungnya menggunakan data Anda sendiri - termasuk sumber daya Pencarian Azure AI.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang valid untuk proyek Anda, dan jika hub yang ada disarankan, pilih opsi untuk membuat yang baru. Kemudian tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung hub dan proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub** : *Nama yang valid untuk hub Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-4o** di jendela pembantu Lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru*
    - **Menyambungkan Pencarian Azure AI**: *Membuat sumber daya Pencarian Azure AI baru dengan nama unik*

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota model regional. Jika batas kuota terlampaui di kemudian hari dalam latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

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
1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Data + Indeks**.
1. Pilih **+ Data baru**.
1. Di wizard **Tambahkan data** Anda, perluas menu drop-down untuk memilih **Unggah file/folder**.
1. Pilih **Unggah folder** dan ungguh folder **brosur**. Tunggu hingga semua berkas dalam folder ditampilkan.
1. Pilih **Berikutnya** dan ubah nama data menjadi `brochures`.
1. Tunggu folder untuk diunggah dan perhatikan bahwa folder tersebut berisi beberapa file .pdf.

## Buat indeks dan muat data Anda.

Setelah menambahkan sumber data ke proyek, Anda dapat menggunakannya untuk membuat indeks di sumber daya Azure AI Search Anda.

1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Data + Indeks**.
1. Tambahkan **Indeks** baru dengan pengaturan berikut:
    - **Lokasi sumber**
        - **Sumber data**: Data di Azure AI Foundry
            - *Pilih ** sumber data brosur** *
    - **Konfigurasi indeks**:
        - **Pilih Azure AI layanan Pencarian**: *Pilih **koneksi AzureAISearch** ke sumber daya Azure AI Search Anda*
        - **Indeks vektor**: `brochures-index`
        - **Mesin virtual**: Pilih otomatis
    - **Pengaturan Pencarian**:
        - **Pengaturan vektor**: Menambahkan pencarian vektor ke sumber daya pencarian ini
        - **Koneksi Azure OpenAI**: *Pilih sumber daya Azure OpenAI default untuk hub Anda.*
        - **Model Penyematan**: text-embedding-ada-002
        - **Penyebaran model penyematan**: *Penyebaran* model text-embedding-ada-002 *Anda*

1. Buat indeks vektor dan tunggu hingga proses pengindeksan selesai, yang dapat memakan waktu cukup lama tergantung pada sumber daya komputasi yang tersedia dalam langganan Anda.

    Operasi pembuatan indeks terdiri dari pekerjaan berikut:

    - Retak, potong, dan sematkan token teks di data brosur Anda.
    - Membuat indeks Pencarian Azure AI
    - Daftarkan aset indeks.

    > **Tips**: Saat Anda menunggu indeks dibuat, mengapa tidak melihat brosur yang Anda unduh untuk membiasakan diri dengan kontennya?

## Menguji indeks di taman bermain

Sebelum menggunakan indeks Anda dalam alur prompt berbasis RAG, mari kita verifikasi bahwa indeks tersebut dapat digunakan untuk memengaruhi respons AI generatif.

1. Pada panel navigasi di sebelah kiri, pilih halaman **Playground** dan buka **Obrolan**.
1. Pada halaman Playground obrolan, di panel Penyiapan, pastikan penyebaran model **gpt-4** Anda dipilih. Kemudian, di panel sesi obrolan utama, kirimkan perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus menjadi jawaban umum dari model tanpa data apa pun dari indeks.
1. Pada panel Penyiapan, pilih tab **Tambahkan data Anda**, lalu tambahkan **indeks-brosur** indeks proyek dan pilih jenis pencarian **hibrid (vektor + kata kunci).**

   > **Tips**: Dalam beberapa kasus, indeks yang baru dibuat mungkin tidak segera tersedia. Menyegarkan browser biasanya membantu, tetapi jika Anda masih mengalami masalah di mana tidak dapat menemukan indeks, Anda mungkin perlu menunggu sampai indeks dikenali.

1. Setelah indeks ditambahkan dan sesi obrolan dimulai ulang, kirim ulang perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus didasarkan pada data dalam indeks.

## Membuat aplikasi klien RAG dengan Azure AI Foundry dan Azure OpenAI SDK

Sekarang setelah Anda memiliki indeks yang berfungsi, Anda dapat menggunakan Azure AI Foundry dan SDK Azure OpenAI untuk menerapkan pola RAG dalam aplikasi klien. Mari kita jelajahi kode untuk mencapainya dalam contoh sederhana.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.

    Tutup pemberitahuan selamat datang apa pun untuk melihat halaman beranda portal Azure.

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

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka yang akan Anda gunakan:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
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
    - **your_project_connection_string**: Ganti dengan string koneksi untuk proyek Anda (disalin dari halaman **Ikhtisar** proyek di portal Azure AI Foundry).
    - **your_gpt_model_deployment** Ganti dengan nama yang Anda tetapkan untuk penerapan model **gpt-4o** Anda.
    - **your_embedding_model_deployment**: Ganti dengan nama yang Anda tetapkan untuk penerapan model **text-embedding-ada-002** Anda.
    - **your_index**: Ganti dengan nama indeks Anda (yang seharusnya `brochures-index`).
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
    - Menggunakan Azure AI Foundry SDK untuk menyambungkan ke proyek Anda (menggunakan string koneksi proyek)
    - Membuat klien Azure OpenAI terautentikasi dari koneksi proyek Anda.
    - Mengambil koneksi Pencarian Azure AI default dari proyek Anda sehingga dapat menentukan titik akhir dan kunci untuk layanan Pencarian Azure AI Anda.
    - Membuat pesan sistem yang sesuai.
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
