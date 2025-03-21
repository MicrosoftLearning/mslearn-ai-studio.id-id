---
lab:
  title: Membuat aplikasi obrolan AI generatif
  description: Pelajari cara menggunakan Azure AI Foundry SDK untuk membangun aplikasi yang terhubung ke proyek dan obrolan Anda dengan model bahasa.
---

# Membuat aplikasi obrolan AI generatif

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi obrolan sederhana yang terhubung ke proyek dan obrolan dengan model bahasa.

Latihan ini memakan waktu sekitar **30** menit.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang sesuai untuk (misalnya, `my-ai-project`) lalu tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub**: *Nama unik - misalnya `my-ai-hub`*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya dengan nama unik (misalnya, `my-ai-resources`), atau pilih yang sudah ada*
    - **Wilayah**: Pilih salah satu wilayah berikut\*:
        - AS Timur
        - AS Timur 2
        - US Tengah Utara
        - US Tengah Selatan
        - Swedia Tengah
        - US Barat
        - AS Barat 3
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru dengan nama yang sesuai (misalnya, `my-ai-services`) atau menggunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Pada saat penulisan, model Microsoft *Phi-4* yang akan kita gunakan dalam latihan ini tersedia di wilayah ini. Anda dapat memeriksa ketersediaan regional terbaru untuk model tertentu dalam [dokumentasi Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

## Menyebarkan model AI generatif

Sekarang Anda telah siap untuk menyebarkan model bahasa AI generatif untuk mendukung aplikasi obrolan Anda. Dalam contoh ini, Anda akan menggunakan model Microsoft Phi-4; tetapi prinsip-prinsipnya sama untuk model apa pun.

1. Di toolbar di kanan atas halaman proyek Azure AI Foundry Anda, gunakan ikon **Fitur pratinjau** untuk mengaktifkan fitur **Sebarkan model ke layanan inferensi model Azure AI**. Fitur ini memastikan penyebaran model Anda tersedia untuk layanan Inferensi Azure AI, yang akan Anda gunakan dalam kode aplikasi Anda.
1. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
1. Pada halaman **Model + titik akhir** , di tab **Penyebaran model** di menu **+ Sebarkan model** pilih **Sebarkan model dasar**.
1. Cari model **Phi-4** dalam daftar, lalu pilih dan konfirmasikan.
1. Setujui perjanjian lisensi jika diminta, lalu sebarkan model dengan pengaturan berikut dengan memilih **Sesuaikan** dalam detail penyebaran:
    - **Nama penyebaran** : *Nama unik untuk penyebaran model Anda - misalnya `phi-4-model` (ingat nama yang Anda tetapkan, Anda akan membutuhkannya nanti*)
    - **Tipe penyebaran**: Standar global
    - **Detail penyebaran**: *Gunakan pengaturan default*
1. Tunggu hingga status penyediaan penyebaran menjadi **Selesai**.

## Membuat aplikasi klien untuk mengobrol dengan model

Setelah menyebarkan model, Anda dapat menggunakan Azure AI Foundry SDK untuk mengembangkan aplikasi yang mengobrol dengannya.

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
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka yang akan Anda gunakan:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
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

1. Dalam file kode, ganti tempat penampung **your_project_endpoint** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry), dan **tempat penampung your_model_deployment** dengan nama yang Anda tetapkan ke penyebaran model Phi-4 Anda.
1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

### Menulis kode untuk menyambungkan ke proyek Anda dan mengobrol dengan model Anda

> **Tips**: Saat Anda menambahkan kode, pastikan untuk mempertahankan indentasi yang benar.

1. Masukkan perintah berikut untuk mengedit file kode yang telah disediakan:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Dalam file kode, perhatikan pernyataan yang sudah ada yang telah ditambahkan di bagian atas file untuk mengimpor namespace SDK yang diperlukan. Kemudian, di bawah komentar **Tambahkan referensi**, tambahkan kode berikut untuk mereferensikan namespace di pustaka yang Anda instal sebelumnya:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Dalam fungsi **utama**, di bawah komentar **Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tentukan dalam file konfigurasi.
1. Di bawah komentar **Inisialisasi klien proyek**, tambahkan kode berikut untuk menghubungkan proyek Azure AI Foundry Anda dengan menggunakan kredensial Azure yang Anda gunakan untuk masuk saat ini:

    **Python**

    ```
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Di bawah komentar **Dapatkan klien obrolan**, tambahkan kode berikut untuk membuat objek klien untuk mengobrol dengan model:

    **Python**

    ```
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

1. Perhatikan bahwa kode menyertakan perulangan untuk memungkinkan pengguna memasukkan perintah hingga mereka memasukkan "berhenti". Selanjutnya, di bagian perulangan, di bawah komentar **Dapatkan penyelesaian obrolan**, tambahkan kode berikut untuk mengirimkan perintah dan mengambil penyelesaian dari model Anda:

    **Python**

    ```python
   response = chat.complete(
       model=model_deployment,
       messages=[
           SystemMessage("You are a helpful AI assistant that answers questions."),
           UserMessage(input_text)
       ])
   print(response.choices[0].message.content
```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages =
           {
               new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions."),
               new ChatRequestUserMessage(input_text),
           }
   };
    
   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

1. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

### Jalankan aplikasi obrolan tersebut

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan aplikasinya:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Saat diminta, masukkan pertanyaan, seperti `What is the fastest animal on Earth?` dan tinjau respons dari model AI generatif Anda.
1. Coba beberapa pertanyaan lagi. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi klien untuk model AI generatif yang Anda sebarkan dalam proyek Azure AI Foundry.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com) di `https://portal.azure.com` tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
