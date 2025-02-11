---
lab:
  title: Membuat aplikasi obrolan dengan Azure AI Foundry SDK
---

# Membuat aplikasi obrolan dengan Azure AI Foundry SDK

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi obrolan sederhana.

Latihan ini memakan waktu sekitar **20** menit.

## Membuat proyek Azure AI Foundry

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup tips atau panel mulai cepat yang dibuka saat pertama kali Anda masuk, dan jika perlu gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat mirip dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang sesuai untuk (misalnya, `my-ai-project`) lalu tinjau sumber daya Azure yang akan dibuat secara otomatis guna mendukung proyek Anda.
1. Pilih **Kustomisasi**, lalu tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub**: *Nama unik - misalnya `my-ai-hub`*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat grup sumber daya baru dengan nama unik (misalnya, `my-ai-resources`), atau pilih grup yang sudah ada*
    - **Lokasi**: Pilih wilayah acak dari daftar berikut ini\*:
        - AS Timur
        - AS Timur 2
        - US Tengah Utara
        - US Tengah Selatan
        - Swedia Tengah
        - US Barat
        - AS Barat 3
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru dengan nama yang sesuai (misalnya, `my-ai-services`) atau menggunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Kuota model dibatasi di tingkat penyewa oleh kuota regional. Memilih wilayah acak membantu mendistribusikan ketersediaan kuota saat beberapa pengguna bekerja di penyewa yang sama. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Pilih **Buat**, lalu tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

## Menyebarkan model AI generatif

Sekarang Anda siap untuk menyebarkan model bahasa AI generatif untuk mendukung aplikasi obrolan Anda. Dalam contoh ini, Anda akan menggunakan model Microsoft Phi-4; tetapi prinsip-prinsipnya sama untuk model apa pun.

1. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
1. Di halaman **Model + titik akhir**, di tab **Penyebaran model**, di menu **+ Menyebarkan model**, pilih **Menyebarkan model dasar**.
1. Cari model **Phi-4** dalam daftar, lalu pilih dan konfirmasikan.
1. Pilih opsi penyebaran **API Tanpa Server dengan Keamanan Konten Azure AI** .
1. Terapkan model dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penyeberan:
    -  **Nama penyebaran**: *Nama unik untuk penyebaran model Anda - misalnya `phi-4-model` (ingat nama yang Anda tetapkan, Anda akan membutuhkannya nanti*)
    - **Filter konten**: Diaktifkan

## Membuat aplikasi klien untuk mengobrol dengan model

Setelah menyebarkan model, Anda dapat menggunakan Azure AI Foundry SDK untuk mengembangkan aplikasi yang mengobrol dengannya.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. Di area **Detail proyek**, perhatikan **String koneksi proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk dengan kredensial Azure Anda jika diminta.
1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell***. Cloud shell menyediakan antarmuka baris perintah di panel di bagian bawah portal Azure.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

1. Di panel PowerShell, masukkan perintah berikut untuk mengkloning repositori GitHub untuk latihan ini:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Setelah repo dikloning, navigasikan ke folder **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python**:

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. Pada panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka Python yang akan Anda gunakan, yaitu:
    - **python-dotenv** : Digunakan untuk memuat pengaturan dari file konfigurasi aplikasi.
    - **azure-identity**: Digunakan untuk mengautentikasi dengan kredensial Id Entra.
    - **azure-ai-projects**: Digunakan untuk bekerja dengan proyek Azure AI Foundry.
    - **azure-ai-inference**: Digunakan untuk mengobrol dengan model AI generatif.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Masukkan perintah berikut untuk mengedit file konfigurasi Python **.env** yang telah disediakan:

    ```
    code .env
    ```

    File dibuka dalam editor kode.

1. Pada file kode, ganti tempat penampung **your_project_endpoint** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure Ai Foundry), dan tempat penampung **your_model_deployment** dengan nama yang Anda tetapkan untuk penyebaran model Phi-4 Anda.
1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

### Menulis kode untuk menyambungkan ke proyek Anda dan mengobrol dengan model Anda

> **Tips**: Saat Anda menambahkan kode ke file kode Python, pastikan untuk mempertahankan indentasi yang benar.

1. Masukkan perintah berikut untuk mengedit file kode Python **chat-app.py** yang telah disediakan:

    ```
    code chat-app.py
    ```

1. Pada file kode, perhatikan pernyataan **import** yang telah ditambahkan di bagian atas file. Kemudian, di bawah komentar **# Tambahkan referensi Proyek AI **, tambahkan kode berikut untuk merujuk ke pustaka Proyek Azure AI:

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. Dalam fungsi **main**, di bawah komentar **# Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode tersebut memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tetapkan dalam file **.env**.
1. Di bawah komentar **# Inisialisasi klien proyek**, tambahkan kode berikut untuk terhubung ke proyek Azure AI Foundry Anda menggunakan kredensial Azure yang saat ini Anda gunakan untuk masuk:

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. Di bawah komentar **# Get a chat client**, tambahkan kode berikut ini untuk membuat objek klien untuk mengobrol dengan model:

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Perhatikan bahwa kode tersebut menyertakan sebuah loop untuk memungkinkan pengguna memasukkan perintah sampai mereka memasukkan “berhenti”. Kemudian di bagian loop, di bawah komentar **# Dapatkan penyelesaian obrolan**, tambahkan kode berikut untuk mengirimkan perintah dan mengambil penyelesaian dari model Anda:

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda pada file kode, lalu gunakan perintah **CTRL+Q** untuk menutup editor kode dan tetap membuka baris perintah cloud shell.

### Jalankan aplikasi obrolan

1. Pada panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan kode Python:

    ```
    python chat-app.py
    ```

1. Saat diminta, masukkan pertanyaan, seperti `What is the fastest animal on Earth?` dan tinjau respons dari model AI generatif Anda.
1. Coba beberapa pertanyaan lagi. Setelah selesai, masukkan `quit` untuk keluar dari program.

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi klien untuk model AI generatif yang Anda terapkan dalam proyek Azure AI Foundry.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com) di `https://portal.azure.com` pada tab browser baru) dan lihat konten grup sumber daya tempat Anda menerapkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
