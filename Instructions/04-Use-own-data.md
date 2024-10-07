---
lab:
  title: Membuat salinan kustom yang menggunakan data Anda sendiri
---

# Membuat salinan kustom yang menggunakan data Anda sendiri

Retrieval Augmented Generation (RAG) adalah teknik yang digunakan untuk membangun aplikasi yang mengintegrasikan data dari sumber data kustom ke dalam permintaan untuk model AI generatif. RAG adalah pola yang umum digunakan untuk mengembangkan *salinan*kustom - aplikasi berbasis obrolan yang menggunakan model bahasa untuk menafsirkan input dan menghasilkan respons yang sesuai.

Dalam latihan ini, Anda akan menggunakan Azure AI Studio untuk mengintegrasikan data kustom ke dalam alur permintaan AI generatif.

Latihan ini memakan waktu sekitar **45** menit.

## Sebelum memulai

Untuk menyelesaikan latihan ini, langganan Azure Anda harus disetujui untuk akses ke layanan Azure OpenAI. Isi [formulir pendaftaran](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) untuk meminta akses ke model Azure OpenAI.

## Membuat sumber daya  Pencarian Azure AI

Solusi salinan Anda akan mengintegrasikan data kustom ke dalam alur permintaan. Untuk mendukung integrasi ini, Anda memerlukan sumber daya Azure AI Search untuk mengindeks data Anda.

1. Di browser web, buka [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda.
1. Di halaman beranda **+ Buat sumber daya** dan pencarian `Azure AI Search`. Kemudian buat sumber daya Azure AI Search baru dengan pengaturan berikut:

    - **Langganan**: *Pilih langganan Azure Anda*
    - **Grup sumber daya**: *Memilih atau membuat grup sumber daya*
    - **Nama layanan**: *Masukkan nama yang unik*
    - **Lokasi**: *Buat **pilihan acak** dari salah satu wilayah berikut*\*
        - Australia Timur
        - Kanada Timur
        - AS Timur
        - AS Timur 2
        - Prancis Tengah
        - Jepang Timur
        - AS Tengah Bagian Utara
        - Swedia Tengah
        - Swiss 
    - **Tingkat harga**: Standar

    > \* Nantinya, Anda akan membuat Azure AI Hub (yang menyertakan layanan Azure OpenAI) di wilayah yang sama dengan sumber daya Azure AI Search Anda. Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat Azure AI hub lain di wilayah yang berbeda.

1. Tunggu hingga penyebaran Azure AI Search Anda selesai.

## Membuat proyek Azure OpenAI

Sekarang Anda siap untuk membuat proyek Azure AI Studio dan sumber daya Azure AI untuk mendukungnya.

1. Di browser web, buka [Azure AI Studio](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda.
1. Pada halaman **Beranda** Azure AI Studio, pilih **+ Proyek baru**. Kemudian, di wizard **Buat proyek** , buat proyek dengan pengaturan berikut:

    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Hub**: *Buat sumber daya dengan pengaturan berikut:*

        - **Nama hub**: *Nama unik*
        - **Langganan Azure**: *Langganan Azure Anda*.
        - **Grup sumber daya**: *Pilih grup sumber daya yang berisi sumber daya Azure AI Search Anda*
        - **Lokasi**: *Pilih lokasi yang sama dengan sumber daya Azure AI Search Anda*
        - **Azure OpenAI**: (Baru) *Autofills dengan nama hub yang Anda pilih*
        - **Pencarian Azure AI**: *pilih sumber daya Pencarian Azure AI Anda*

1. Tunggu proyek Anda dibuat.

## Terapkan model

Anda memerlukan dua model untuk mengimplementasikan solusi Anda:

- Model *penyematan* untuk memvektorisasi data teks untuk pengindeksan dan pemrosesan yang efisien.
- Model yang dapat menghasilkan respons bahasa alami terhadap pertanyaan berdasarkan data Anda.

1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Penyebaran** .
1. Kemudian buat penyebaran model dasar baru dari **model text-embedding-ada-002** dengan pengaturan berikut:

    - **Nama penyebaran**: `text-embedding-ada-002`
    - **Versi model**: *Default*
    - **Opsi tingkat lanjut**
        - **Filter konten**: *Default*
        - **Batas tarif token per menit**: `5K`
1. Ulangi langkah-langkah sebelumnya untuk menyebarkan model **gpt-35-turbo-16k** dengan nama penyebaran `gpt-35-turbo-16k`.

    > **Catatan**: Mengurangi Token Per Menit (TPM) membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 5.000 TPM cukup untuk data yang digunakan dalam latihan ini.

## Menambahkan data ke proyek Anda

Data untuk salinan Anda terdiri dari serangkaian brosur perjalanan dalam format PDF dari agen perjalanan fiktif *Margie's Travel*. Mari kita tambahkan ke proyek.

1. Unduh [arsip zip brosur](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) dari `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` dan ekstrak ke folder bernama **brosur** pada sistem file lokal Anda.
1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Data** .
1. Pilih **+ Data baru**.
1. Di wizard **Tambahkan data** Anda, perluas menu drop-down untuk memilih **Unggah file/folder**.
1. Pilih **Unggah folder** dan pilih folder **brosur** .
1. Atur nama data `brochures`.
1. Tunggu folder tidak diunggah dan perhatikan bahwa folder tersebut berisi beberapa file .pdf.

## Buat indeks dan muat data Anda.

Setelah menambahkan sumber data ke proyek, Anda dapat menggunakannya untuk membuat indeks di sumber daya Azure AI Search Anda.

1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Indeks** .
1. Tambahkan indeks baru dengan pengaturan berikut:
    - **Sumber data**:
        - **Sumber data**: Data di Azure AI Studio
            - *Pilih ** sumber data brosur** *
    - **Pengaturan indeks**:
        - **Pilih Azure AI layanan Pencarian**: *Pilih **koneksi AzureAISearch** ke sumber daya Azure AI Search Anda*
        - **Nama indeks**: `brochures-index`
        - **Mesin virtual**: Pilih otomatis
    - **Pengaturan Pencarian**:
        - **Pengaturan vektor**: Menambahkan pencarian vektor ke sumber daya pencarian ini
        - **Pilih model penyematan**: *Pilih sumber daya Azure OpenAI default untuk hub Anda.*
        
1. Tunggu hingga proses pengindeksan selesai, yang dapat memakan waktu beberapa menit. Operasi pembuatan indeks terdiri dari pekerjaan berikut:

    - Retak, potong, dan sematkan token teks di data brosur Anda.
    - Membuat indeks Pencarian Azure AI
    - Daftarkan aset indeks.

## Uji indeks

Sebelum menggunakan indeks Anda dalam alur prompt berbasis RAG, mari kita verifikasi bahwa indeks tersebut dapat digunakan untuk memengaruhi respons AI generatif.

1. Di panel navigasi di sebelah kiri, di bawah **Proyek taman bermain**, pilih halaman **Obrolan** .
1. Pada halaman Obrolan, di panel Opsi, pastikan penyebaran model **gpt-35-turbo-16k** Anda dipilih. Kemudian, di panel sesi obrolan utama, kirimkan perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus menjadi jawaban umum dari model tanpa data apa pun dari indeks.
1. Pada panel Penyiapan, pilih tab **Tambahkan data Anda** , lalu tambahkan **indeks-brosur** indeks proyek dan pilih jenis pencarian **hibrid (vektor + kata kunci).** 

   > **Catatan**: Beberapa pengguna segera menemukan indeks yang baru dibuat tidak tersedia. Menyegarkan browser biasanya membantu, tetapi jika Anda masih mengalami masalah di mana tidak dapat menemukan indeks, Anda mungkin perlu menunggu sampai indeks dikenali.
   
1. Setelah indeks ditambahkan dan sesi obrolan dimulai ulang, kirim ulang perintah `Where can I stay in New York?`
1. Tinjau respons, yang harus didasarkan pada data dalam indeks.

## Menggunakan indeks dalam alur perintah

Indeks vektor Anda telah disimpan di proyek Azure AI Studio, memungkinkan Anda menggunakannya dengan mudah dalam alur perintah.

1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Alat**, pilih halaman **Alur permintaan** 
1. Buat alur permintaan baru dengan mengkloning **Tanya Jawab Multi-Putaran pada sampel Data** Anda di galeri. Simpan klon sampel ini di folder bernama `brochure-flow`.
    <details>  
      <summary><b>Tips pemecahan masalah</b>: Kesalahan izin</summary>
        <p>Jika Anda menerima kesalahan izin saat membuat alur permintaan baru, coba langkah berikut untuk memecahkan masalah:</p>
        <ul>
          <li>Di portal Azure, pilih sumber daya Layanan AI.</li>
          <li>Pada halaman IAM, di tab Identitas, konfirmasikan bahwa itu adalah identitas terkelola yang ditetapkan sistem.</li>
          <li>Navigasikan ke Akun Penyimpanan. Pada halaman IAM, tambahkan penetapan peran <em>Pembaca data blob penyimpanan</em>.</li>
          <li>Di bawah <strong>Tetapkan akses ke</strong>, pilih <strong>Identitas Terkelola</strong>, <strong>+ Pilih anggota</strong>, dan pilih <strong>Semua identitas terkelola yang ditetapkan sistem</strong>.</li>
          <li>Tinjau dan tetapkan untuk menyimpan pengaturan baru dan coba lagi langkah sebelumnya.</li>
        </ul>
    </details>

1. Saat halaman perancang alur perintah terbuka, tinjau **alur-brosur**. Jendela Anda akan terlihat seperti gambar berikut:

    ![Cuplikan layar grafik alur perintah](./media/chat-flow.png)

    Alur permintaan sampel yang Anda gunakan mengimplementasikan logika permintaan untuk aplikasi obrolan di mana pengguna dapat secara berulang mengirimkan input teks ke antarmuka obrolan. Riwayat percakapan dipertahankan dan disertakan dalam konteks untuk setiap iterasi. Alur perintah mengatur urutan *alat* untuk:

    - Tambahkan riwayat ke input obrolan untuk menentukan perintah dalam bentuk pertanyaan kontekstual.
    - Ambil konteks menggunakan indeks Anda dan jenis kueri pilihan Anda sendiri berdasarkan pertanyaan.
    - Hasilkan konteks prompt dengan menggunakan data yang diambil dari indeks untuk menambah pertanyaan.
    - Buat varian perintah dengan menambahkan pesan sistem dan menyusun riwayat obrolan.
    - Kirim permintaan ke model bahasa untuk menghasilkan respons bahasa alami.

1. Gunakan tombol **Mulai sesi komputasi** untuk memulai komputasi runtime untuk alur.

    Tunggu hingga runtime dimulai. Ini menyediakan konteks komputasi untuk alur perintah. Saat Anda menunggu, di tab **Alur** , tinjau bagian untuk alat dalam alur.

1. Di bagian **Input** , pastikan input meliputi:
    - **riwayat_obrolan**
    - **input_obrolan**

    Riwayat obrolan default dalam sampel ini mencakup beberapa percakapan tentang AI.

1. Di bagian **Output** , pastikan bahwa output mencakup:

    - **output_chat** dengan nilai ${chat_with_context.output}

1. Di bagian **modify_query_with_history** , pilih pengaturan berikut (biarkan orang lain apa adanya):

    - **Koneksi**: *Sumber daya Azure OpenAI default untuk hub AI Anda*
    - **Api**: obrolan
    - **nama penyebaran**: gpt-35-turbo-16k
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

    - **Koneksi**: Default_AzureOpenAI
    - **Api**: Obrolan
    - **nama penyebaran**: gpt-35-turbo-16k
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

## Sebarkan alur

Sekarang setelah Anda memiliki alur kerja yang menggunakan data terindeks, Anda dapat menyebarkannya sebagai layanan untuk dikonsumsi oleh aplikasi salinan.

> **Catatan**: Tergantung pada wilayah dan beban pusat data, penyebaran terkadang dapat memakan waktu cukup lama. Jangan ragu untuk beralih ke bagian tantangan di bawah ini saat menyebarkan atau melewati pengujian penyebaran Anda jika Anda kekurangan waktu.

1. Di toolbar, pilih **Sebarkan**.
1. Buat penyebaran baru dengan pengaturan berikut:
    - **Pengaturan dasar**:
        - **Titik akhir**: Baru
        - **Nama titik akhir**: *Gunakan nama titik akhir unik default*
        - **Nama penyebaran**: *Gunakan nama titik akhir penyebaran default*
        - **Komputer virtual**: Standard_DS3_v2
        - **Jumlah instans**: 3
        - **Pengumpulan data inferensi**: Dipilih
    - **Pengaturan tingkat lanjut**:
        - *Menjaga pengaturan default*
1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Penyebaran** .
1. Terus refresh tampilan hingga **penyebaran brosur-endpoint-1** ditampilkan sebagai telah *berhasil* di bawah **titik akhir titik** akhir brosur (ini mungkin memakan waktu yang signifikan).
1. Ketika penyebaran telah berhasil, pilih penyebaran tersebut. Kemudian, pada halaman **Uji** , masukkan perintah `What is there to do in San Francisco?` dan tinjau responsnya.
1. Masukkan perintah `Where else could I go?` dan tinjau responsnya.
1. Lihat halaman **Konsumsi** untuk titik akhir, dan perhatikan bahwa halaman tersebut berisi informasi koneksi dan kode sampel yang dapat Anda gunakan untuk membangun aplikasi klien untuk titik akhir Anda - memungkinkan Anda mengintegrasikan solusi alur perintah ke dalam aplikasi sebagai salinan kustom.

## Latihan 

Sekarang Anda telah mengalami cara mengintegrasikan data Anda sendiri dalam salinan yang dibangun dengan Azure AI Studio, mari kita jelajahi lebih lanjut!

Coba tambahkan sumber data baru melalui Azure AI Studio, indeks, dan integrasikan data terindeks dalam alur perintah. Beberapa himpunan data yang dapat Anda coba adalah:

- Kumpulan artikel (penelitian) yang Anda miliki di komputer Anda.
- Sekumpulan presentasi dari konferensi sebelumnya.
- Salah satu himpunan data yang tersedia di repositori [sampel data Pencarian Azure](https://github.com/Azure-Samples/azure-search-sample-data) .

Jadilah sebagai sumber daya yang Anda bisa untuk membuat sumber data Anda dan mengintegrasikannya dalam alur permintaan Anda. Cobalah alur permintaan baru dan kirimkan perintah yang hanya dapat dijawab oleh himpunan data yang Anda pilih!

## Penghapusan

Untuk menghindari biaya Azure dan pemanfaatan sumber daya yang tidak perlu, Anda harus menghapus sumber daya yang Anda sebarkan dalam latihan ini.

1. Jika Anda telah selesai menjelajahi Azure AI Studio, kembali ke [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda jika perlu. Kemudian hapus sumber daya di grup sumber daya tempat Anda memprovisikan sumber daya Azure AI Search dan Azure AI Anda.