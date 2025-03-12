---
lab:
  title: Membuat aplikasi AI generatif yang menggunakan data Anda sendiri
  description: Pelajari cara menggunakan model Retrieval Augmented Generation (RAG) untuk membuat aplikasi obrolan yang mendasarkan perintah dengan menggunakan data Anda sendiri.
---

# Membuat aplikasi AI generatif yang menggunakan data Anda sendiri

Retrieval Augmented Generation (RAG) adalah teknik yang digunakan untuk membangun aplikasi yang mengintegrasikan data dari sumber data kustom ke dalam permintaan untuk model AI generatif. RAG adalah pola yang umum digunakan untuk mengembangkan aplikasi AI generatif - aplikasi berbasis obrolan yang menggunakan model bahasa untuk menafsirkan input dan menghasilkan respons yang sesuai.

Dalam latihan ini, Anda akan menggunakan Azure AI Foundry untuk mengintegrasikan data kustom ke dalam alur permintaan AI generatif. Anda juga akan menjelajahi cara menerapkan pola RAG di aplikasi klien dengan menggunakan Azure AI Foundry dan Azure OpenAI SDK.

Latihan ini memakan waktu sekitar **45** menit.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry dan sumber daya layanan yang perlu didukungnya menggunakan data Anda sendiri - termasuk sumber daya Pencarian Azure AI.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang sesuai untuk (misalnya, `my-ai-project`) lalu tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub**: *Nama unik - misalnya `my-ai-hub`*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya dengan nama unik (misalnya, `my-ai-resources`), atau pilih yang sudah ada*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-4** dan **text-embedding-ada-002** di jendela Pembantu Lokasi dan gunakan wilayah yang disarankan\*
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru dengan nama yang sesuai (misalnya, `my-ai-services`) atau menggunakan yang sudah ada*
    - **Menyambungkan Pencarian Azure AI**: *Membuat sumber daya Pencarian Azure AI baru dengan nama unik*

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek **Ringkasan** di Portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)
   
## Terapkan model

Anda memerlukan dua model untuk mengimplementasikan solusi Anda:

- Model *penyematan* untuk memvektorisasi data teks untuk pengindeksan dan pemrosesan yang efisien.
- Model yang dapat menghasilkan respons bahasa alami terhadap pertanyaan berdasarkan data Anda.

1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Buat penyebaran baru model **text-embedding-ada-002** dengan pengaturan berikut dengan memilih **Sesuaikan** di wizard Penerapan model:

    - **Nama penyebaran**: `text-embedding-ada-002`
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > **Catatan**: Jika lokasi sumber daya AI Anda saat ini tidak memiliki kuota yang tersedia untuk model yang ingin Anda terapkan, Anda akan diminta untuk memilih lokasi lain tempat sumber daya AI baru akan dibuat dan tersambung ke proyek Anda.

1. Ulangi langkah-langkah sebelumnya untuk menyebarkan model **gpt-4** dengan nama penyebaran `gpt-4`.

    > **Catatan**: Mengurangi Token Per Menit (TPM) membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 5.000 TPM cukup untuk data yang digunakan dalam latihan ini.

## Menambahkan data ke proyek Anda

Data untuk salinan Anda terdiri dari serangkaian brosur perjalanan dalam format PDF dari agen perjalanan fiktif *Margie's Travel*. Mari kita tambahkan ke proyek.

1. Unduh [arsip zip brosur](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) dari `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` dan ekstrak ke folder bernama **brosur** pada sistem file lokal Anda.
1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Data + Indeks**.
1. Pilih **+ Data baru**.
1. Di wizard **Tambahkan data** Anda, perluas menu drop-down untuk memilih **Unggah file/folder**.
1. Pilih **Unggah folder** dan pilih folder **brosur** .
1. Pilih **Berikutnya** dan ubah nama data menjadi `brochures`.
1. Tunggu folder untuk diunggah dan perhatikan bahwa folder tersebut berisi beberapa file .pdf.

## Buat indeks dan muat data Anda.

Setelah menambahkan sumber data ke proyek, Anda dapat menggunakannya untuk membuat indeks di sumber daya Azure AI Search Anda.

1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Data + Indeks**.
1. Tambahkan **Indeks** baru dengan pengaturan berikut:
    - **Lokasi sumber**
        - **Sumber data**: Data di portal Azure AI Foundry
            - *Pilih ** sumber data brosur** *
    - **Konfigurasi indeks**:
        - **Pilih Azure AI layanan Pencarian**: *Pilih **koneksi AzureAISearch** ke sumber daya Azure AI Search Anda*
        - **Indeks vektor**: `brochures-index`
        - **Mesin virtual**: Pilih otomatis
    - **Pengaturan Pencarian**:
        - **Pengaturan vektor**: Menambahkan pencarian vektor ke sumber daya pencarian ini
        - **Koneksi Azure OpenAI**: *Pilih sumber daya Azure OpenAI default untuk hub Anda.*

1. Tunggu hingga proses pengindeksan selesai, yang dapat memakan waktu beberapa menit. Operasi pembuatan indeks terdiri dari pekerjaan berikut:

    - Retak, potong, dan sematkan token teks di data brosur Anda.
    - Membuat indeks Pencarian Azure AI
    - Daftarkan aset indeks.

## Uji indeks

Sebelum menggunakan indeks Anda dalam alur prompt berbasis RAG, mari kita verifikasi bahwa indeks tersebut dapat digunakan untuk memengaruhi respons AI generatif.

1. Di panel navigasi di sebelah kiri, di bawah halaman **Playground**.
1. Pada halaman Obrolan, di panel Penyiapan, pastikan penyebaran model **gpt-4** Anda terpilih. Kemudian, di panel sesi obrolan utama, kirimkan perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus menjadi jawaban umum dari model tanpa data apa pun dari indeks.
1. Pada panel Penyiapan, pilih tab **Tambahkan data Anda**, lalu tambahkan **indeks-brosur** indeks proyek dan pilih jenis pencarian **hibrid (vektor + kata kunci).**

   > **Catatan**: Beberapa pengguna segera menemukan indeks yang baru dibuat tidak tersedia. Menyegarkan browser biasanya membantu, tetapi jika Anda masih mengalami masalah di mana tidak dapat menemukan indeks, Anda mungkin perlu menunggu sampai indeks dikenali.

1. Setelah indeks ditambahkan dan sesi obrolan dimulai ulang, kirim ulang perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus didasarkan pada data dalam indeks.

## Menggunakan indeks dalam alur perintah

Indeks vektor Anda telah disimpan di proyek Azure AI Foundry, memungkinkan Anda menggunakannya dengan mudah dalam alur perintah.

1. Di Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Membangun dan menyesuaikan**, pilih halaman **Alur perintah**
1. Buat alur permintaan baru dengan mengkloning **Tanya Jawab Multi-Putaran pada sampel Data** Anda di galeri. Simpan klon sampel ini di folder bernama `brochure-flow`.

    <details> 
        <summary><font color="red"><b>Tips pemecahan masalah</b>: Kesalahan izin</font></summary>
        <p>Jika Anda menerima kesalahan izin saat membuat alur perintah baru, coba langkah berikut untuk memecahkan masalah:</p>
        <ul>
            <li>Di portal Azure, di grup sumber daya untuk hub Azure AI Foundry Anda, pilih sumber daya Layanan AI.</li>
            <li>Pada <b>Manajemen Sumber Daya</b>, di tab <b>Identitas</b>, pastikan bahwa status <b>Sistem yang ditetapkan</b> identitas terkelola adalah <b>Aktif</b>.</li>
            <li>Di grup sumber daya untuk hub Azure AI Foundry Anda, pilih Akun Penyimpanan</li>
            <li>Pada halaman <b>Kontrol akses (IAM),</b> tambahkan penetapan peran untuk menetapkan peran <b>Pembaca data blob Penyimpanan</b> ke Identitas terkelola untuk sumber daya layanan Azure AI Anda.</li>
            <li>Tunggu hingga peran ditetapkan, lalu coba lagi langkah sebelumnya.</li>
        </ul>
    </details>

1. Saat halaman perancang alur perintah terbuka, tinjau **alur-brosur**. Jendela Anda akan terlihat seperti gambar berikut:

    ![Tangkapan layar grafik alur perintah](./media/chat-flow.png)

    Alur permintaan sampel yang Anda gunakan mengimplementasikan logika permintaan untuk aplikasi obrolan di mana pengguna dapat secara berulang mengirimkan input teks ke antarmuka obrolan. Riwayat percakapan dipertahankan dan disertakan dalam konteks untuk setiap iterasi. Alur perintah mengatur urutan *alat* untuk:

    - Tambahkan riwayat ke input obrolan untuk menentukan perintah dalam bentuk pertanyaan kontekstual.
    - Ambil konteks menggunakan indeks Anda dan jenis kueri pilihan Anda sendiri berdasarkan pertanyaan.
    - Hasilkan konteks prompt dengan menggunakan data yang diambil dari indeks untuk menambah pertanyaan.
    - Buat varian perintah dengan menambahkan pesan sistem dan menyusun riwayat obrolan.
    - Kirim permintaan ke model bahasa untuk menghasilkan respons bahasa alami.

1. Gunakan tombol **Mulai sesi komputasi** untuk memulai komputasi runtime untuk alur.

    Tunggu hingga sesi komputasi dimulai. Ini menyediakan konteks komputasi untuk alur perintah. Saat Anda menunggu, di tab **Alur** , tinjau bagian untuk alat dalam alur.

    >**Catatan**: Karena keterbatasan infrastruktur dan kapasitas, sesi komputasi mungkin gagal dimulai selama periode permintaan tinggi. Jika itu terjadi, Anda dapat melewati menggunakan alur prompt dan memulai tugas **Membuat aplikasi klien RAG dengan Azure AI Foundry dan Azure OpenAI SDK**.

    Kemudian, ketika sesi komputasi telah dimulai...

1. Di bagian **Input** , pastikan input meliputi:
    - **riwayat_obrolan**
    - **input_obrolan**

    Riwayat obrolan default dalam sampel ini mencakup beberapa percakapan tentang AI.

1. Di bagian **Output** , pastikan bahwa output mencakup:

    - **output_chat** dengan nilai ${chat_with_context.output}

1. Di bagian **modify_query_with_history** , pilih pengaturan berikut (biarkan orang lain apa adanya):

    - **Koneksi**: *Sumber daya Azure OpenAI default untuk hub AI Anda*
    - **Api**: obrolan
    - **deployment_name**: gpt-4
    - **format_respon**: {"type":"text"}

1. Di bagian **pencarian** , atur nilai parameter berikut:

    - **mlindex_content**: *Pilih bidang kosong untuk membuka panel Hasilkan*
        - **index_type**: Indeks Terdaftar
        - **mlindex_asset_id**: brosur-indeks:1
    - **kueri**: ${modify_query_with_history.output}
    - **query_type**: Hibrid (vektor + kata kunci)
    - **top_k**: 2

1. Di bagian **generate_prompt_context** , tinjau skrip Python dan pastikan bahwa **input** untuk alat ini menyertakan parameter berikut:

    - **hasil_pencarian***(objek)*: ${lookup.output}

1. Di bagian **Prompt_variants** , tinjau skrip Python dan pastikan bahwa **input** untuk alat ini menyertakan parameter berikut:

    - **konteks***(string)*: ${generate_prompt_context.output}
    - **riwayat_obrolan***(string)*: ${inputs.chat_history}
    - **input_obrolan***(string)*: ${inputs.chat_input}

1. Di bagian **chat_with_context** , pilih pengaturan berikut (biarkan orang lain apa adanya):

    - **Koneksi**: *Sumber daya Azure OpenAI default untuk hub AI Anda*
    - **Api**: Obrolan
    - **deployment_name**: gpt-4
    - **format_respon**: {"type":"text"}

    Kemudian pastikan bahwa **input** untuk alat ini menyertakan parameter berikut:
    - **teks_perintah***(string)*: ${Prompt_variants.output}

1. Pada toolbar, gunakan tombol **Simpan** untuk menyimpan perubahan yang telah Anda buat ke alat dalam alur perintah.
1. Pada toolbar, pilih **Obrolan**. Panel obrolan terbuka dengan riwayat percakapan sampel dan input yang sudah diisi berdasarkan nilai sampel. Anda dapat mengabaikan ini.
1. Di panel obrolan, ganti input default dengan pertanyaan `Where can I stay in London?` dan kirimkan.
1. Tinjau respons, yang harus didasarkan pada data dalam indeks.
1. Tinjau output untuk setiap alat dalam alur.
1. Di panel obrolan, masukkan pertanyaan `What can I do there?`
1. Tinjau respons, yang harus didasarkan pada data dalam indeks dan mempertimbangkan riwayat obrolan (sehingga "ada" dipahami sebagai "di London").
1. Tinjau output untuk setiap alat dalam alur, mencatat bagaimana setiap alat dalam alur beroperasi pada inputnya untuk menyiapkan permintaan kontekstual dan mendapatkan respons yang sesuai.

    Anda sekarang memiliki alur prompt kerja yang menggunakan indeks Azure AI Search Anda untuk menerapkan pola RAG. Untuk mempelajari selengkapnya tentang cara menyebarkan dan mengonsumsi alur prompt Anda, lihat [dokumentasi Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/how-to/flow-deploy).

## Membuat aplikasi klien RAG dengan Azure AI Foundry dan Azure OpenAI SDK

Meskipun alur prompt adalah cara yang bagus untuk menyertakan model dan data Anda dalam aplikasi berbasis RAG, Anda juga dapat menggunakan Azure AI Foundry dan Azure OpenAI SDK untuk mengimplementasikan pola RAG dalam aplikasi klien. Mari kita jelajahi kode untuk mencapainya dalam contoh sederhana.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.
1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell***. Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    > **Tips**: Saat Anda menempelkan perintah ke cloudshell, ouput mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

1. Di panel PowerShell, masukkan perintah berikut untuk mengkloning repositori GitHub untuk latihan ini:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Catatan**: Ikuti langkah-langkah untuk bahasa pemrograman yang Anda pilih.

1. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode aplikasi obrolan:  

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
    - **your_project_endpoint**: Ganti dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry)
    - ** your_model_deployment** Ganti dengan nama yang Anda tetapkan ke penyebaran model Anda (yang seharusnya `gpt-4`)
    - **your_index**: Ganti dengan nama indeks Anda (yang seharusnya `brochures-index`)
1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

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
    - Mengambil koneksi Pencarian Azure AI default dari proyek Anda sehingga dapat menentukan titik akhir dan kunci untuk layanan Pencarian Azure AI Anda.
    - Membuat klien Azure OpenAI yang diautentikasi berdasarkan koneksi layanan Azure OpenAI default dalam proyek Anda.
    - Mengirimkan perintah (termasuk sistem dan pesan pengguna) ke klien Azure OpenAI, menambahkan informasi tambahan tentang indeks Pencarian Azure AI yang akan digunakan untuk mendasarkan perintah.
    - Menampilkan respons dari perintah yang didasarkan.
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

1. Saat diminta, masukkan pertanyaan, seperti `Where can I travel to?` dan tinjau respons dari model AI generatif Anda.

    Perhatikan bahwa respons menyertakan referensi sumber untuk menunjukkan data terindeks tempat jawaban ditemukan.

1. Coba beberapa pertanyaan lagi, misalnya `Where should I stay in London?`

    > **Catatan**: Aplikasi contoh sederhana ini tidak menyertakan logika apa pun untuk mempertahankan riwayat percakapan, sehingga setiap permintaan diperlakukan sebagai percakapan baru.

1. Setelah selesai, tekan enter `quit` untuk mengakhiri program. Lalu tutup panel Cloud Shell.

## Latihan

Sekarang Anda telah merasakan bagaimana memadukan data Anda sendiri dalam aplikasi AI generatif yang dibangun dengan portal Azure AI Foundry, mari jelajahi lebih lanjut!

Coba tambahkan sumber data baru melalui portal Azure AI Foundry, indeks, dan integrasikan data yang diindeks dalam alur perintah. Beberapa himpunan data yang dapat Anda coba adalah:

- Kumpulan artikel (penelitian) yang Anda miliki di komputer Anda.
- Sekumpulan presentasi dari konferensi sebelumnya.
- Salah satu himpunan data yang tersedia di repositori [sampel data Pencarian Azure](https://github.com/Azure-Samples/azure-search-sample-data) .

Jadilah sekreatif sebisa Anda untuk membuat sumber data Anda dan mengintegrasikannya dalam alur prompt atau aplikasi klien Anda. Uji solusi Anda dengan mengirimkan perintah yang hanya dapat dijawab oleh himpunan data yang Anda pilih!

## Penghapusan

Untuk menghindari biaya Azure dan pemanfaatan sumber daya yang tidak perlu, Anda harus menghapus sumber daya yang Anda sebarkan dalam latihan ini.

1. Jika Anda telah selesai menjelajahi Azure AI Foundry, kembali ke [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda jika perlu. Kemudian hapus sumber daya di grup sumber daya tempat Anda memprovisikan sumber daya Azure AI Search dan Azure AI Anda.
