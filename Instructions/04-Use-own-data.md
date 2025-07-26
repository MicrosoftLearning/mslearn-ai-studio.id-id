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

## Membuat hub dan proyek di Azure AI Foundry

Fitur Azure AI Foundry yang akan kita gunakan dalam latihan ini memerlukan proyek yang didasarkan pada sumber daya *hub* Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tips atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke beranda, yang tampilannya mirip dengan gambar berikut (tutup panel **Bantuan** jika terbuka):

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di browser, navigasikan ke `https://ai.azure.com/managementCenter/allResources` dan pilih **Buat baru**. Lalu pilih opsi untuk membuat **sumber daya hub AI** baru.
1. Di wizard **Buat proyek** masukkan nama yang valid untuk proyek Anda dan pilih opsi untuk membut hub baru. Kemudian gunakan tautan **Ganti nama hub** untuk menentukan nama yang valid untuk hub baru Anda, perluas **opsi Tingkat lanjut**, dan tentukan pengaturan berikut untuk proyek Anda:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Lokasi**: AS Timur 2 atau Swedia Tengah (*Jika kemudian terjadi batas kuota telampau saat latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda*)

    > **Catatan**: Jika Anda bekerja dengan berlangganan Azure di mana kebijakan digunakan untuk membatasi nama sumber daya yang diizinkan, Anda mungkin perlu menggunakan tautan di bagian bawah kotak dialog **untuk Membuat proyek baru** guna membuat hub menggunakan portal Azure.

    > **Tips**: Jika tombol **Buat** masih dinonaktifkan, pastikan untuk mengganti nama hub Anda menjadi nilai alfanumerik unik.

1. Tunggu hingga proyek Anda dibuat, lalu navigasikan ke proyek Anda.

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
        - **Pilih layanan Pencarian Azure AI**: *Buat sumber daya Azure AI Search baru dengan pengaturan* berikut:
            - **Langganan**: *Langganan Azure Anda*
            - **Grup sumber daya**: *Grup sumber daya yang sama dengan hub AI Anda*
            - **Nama layanan**: *Nama yang valid untuk Sumber Daya AI Search*
            - **Lokasi**: *Lokasi yang sama dengan hub AI Anda*
            - **Tingkat harga**: Dasar
            
            Tunggu hingga sumber daya AI Search dibuat. Kemudian kembali ke Azure AI Foundry dan selesaikan konfigurasi indeks dengan memilih **Hubungkan sumber daya Azure AI Search lainnya** dan tambahkan koneksi ke sumber daya AI Search yang baru saja Anda buat.
 
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
    - **your_openai_endpoint**: Titik akhir Open AI dari halaman **Sekilas** proyek Anda di portal Azure AI Foundry (pastikan untuk memilih tab kemampuan **Azure OpenAI**, bukan kemampuan Azure AI Inference atau Azure AI Service).
    - **your_openai_api_key** Kunci API Open AI dari halaman **Sekilas** proyek Anda di portal Azure AI Foundry (pastikan untuk memilih tab kemampuan **Azure OpenAI**, bukan kemampuan Azure AI Inference atau Azure AI Service).
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

    > **Tips**: Jika kesalahan kompilasi terjadi karena .NET versi 9.0 tidak diinstal, gunakan perintah `dotnet --version` untuk menentukan versi .NET yang diinstal di lingkungan Anda, lalu edit file **rag_app.csproj** di folder kode untuk memperbarui pengaturan **TargetFramework** yang sesuai.

1. Saat diminta, masukkan pertanyaan, seperti `Where should I go on vacation to see architecture?` dan tinjau respons dari model AI generatif Anda.

    Perhatikan bahwa respons menyertakan referensi sumber untuk menunjukkan data terindeks tempat jawaban ditemukan.

1. Coba pertanyaan tindak lanjut, misalnya `Where can I stay there?`

1. Setelah selesai, tekan enter `quit` untuk mengakhiri program. Lalu tutup panel Cloud Shell.

## Penghapusan

Untuk menghindari biaya Azure dan pemanfaatan sumber daya yang tidak perlu, Anda harus menghapus sumber daya yang Anda sebarkan dalam latihan ini.

1. Jika Anda telah selesai menjelajahi Azure AI Foundry, kembali ke [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda jika perlu. Kemudian hapus sumber daya di grup sumber daya tempat Anda memprovisikan sumber daya Azure AI Search dan Azure AI Anda.
