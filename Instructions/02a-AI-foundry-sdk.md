---
lab:
  title: Membuat aplikasi obrolan AI generatif
  description: Pelajari cara menggunakan Azure AI Foundry SDK untuk membangun aplikasi yang terhubung ke proyek dan obrolan Anda dengan model bahasa.
---

# Membuat aplikasi obrolan AI generatif

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi obrolan sederhana yang terhubung ke proyek dan obrolan dengan model bahasa.

Latihan ini memakan waktu sekitar **40** menit.

> **Catatan**: Latihan ini didasarkan pada SDK pra-rilis, yang mungkin dapat berubah. Jika perlu, kami telah menggunakan versi paket tertentu; yang mungkin tidak mencerminkan versi terbaru yang tersedia. Anda mungkin mengalami beberapa perilaku, peringatan, atau kesalahan tak terduga.

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
    - **Wilayah**: *Pilih **Lokasi yang didukung Layanan AI***\*

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota model regional. Jika batas kuota terlampaui di kemudian hari dalam latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Buat** dan tunggu untuk proyek Anda, termasuk penyebaran model gpt-4 yang dipilih untuk dibuat.
1. Saat proyek Anda dibuat,ruang obrolan akan dibuka secara otomatis.
1. Di panel **Penyiapan** , perhatikan nama penyebaran model Anda; yang seharusnya **gpt-4o**. Anda dapat mengonfirmasi ini dengan melihat penyebaran di **halaman Model dan titik** akhir (cukup buka halaman tersebut di panel navigasi di sebelah kiri).
1. Di panel navigasi di sebelah kiri, pilih **Gambaran Umum** untuk melihat halaman utama untuk proyek Anda; yang terlihat seperti ini:

    ![Tangkapan layar halaman gambaran umum proyek Azure AI Foundry .](./media/ai-foundry-project.png)

## Membuat aplikasi klien untuk mengobrol dengan model

Sekarang setelah Anda menerapkan model, Anda dapat menggunakan Azure AI Foundry dan SDK inferensi model Azure AI untuk mengembangkan aplikasi yang mengobrol dengannya.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. **Di area detail** Proyek, perhatikan **titik** akhir proyek Azure AI Foundry. Anda akan menggunakan titik akhir ini untuk menyambungkan ke proyek Anda di aplikasi klien.
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

    > **Tips**: Saat Anda memasukkan perintah ke cloudshell, ouputnya mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

1. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode aplikasi obrolan:

    Gunakan perintah di bawah ini tergantung pada pilihan bahasa pemrograman Anda.

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
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.9
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.5
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

1. Dalam file kode, ganti tempat penampung **your_project_endpoint** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry), dan **tempat penampung your_model_deployment** dengan nama yang Anda tentukan ke penyebaran model gpt-4.
1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** atau **Klik kanan > Simpan** untuk menyimpan perubahan Anda dan kemudian gunakan perintah **CTRL+Q** atau **Klik kanan > Keluar** untuk menutup editor kode sambil tetap membuka baris perintah cloud shell.

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

1. Dalam file kode, perhatikan pernyataan yang sudah ada yang telah ditambahkan di bagian atas file untuk mengimpor namespace SDK yang diperlukan. Kemudian, di bawah komentar **Tambahkan referensi**, dan tambahkan kode berikut untuk mereferensikan kumpulan nama di pustaka yang Anda instal sebelumnya:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from urllib.parse import urlparse
   from azure.identity import DefaultAzureCredential
   from azure.ai.inference import ChatCompletionsClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Dalam fungsi **utama**, di bawah komentar **Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tentukan dalam file konfigurasi.
1. Temukan komentar **Dapatkan klien obrolan**, tambahkan kode berikut untuk membuat objek klien untuk mengobrol dengan model:

    > **Tips**: Berhati-hatilah untuk mempertahankan tingkat indentasi yang benar untuk kode Anda.

    **Python**

    ```python
   # Get a chat client
   inference_endpoint = f"https://{urlparse(project_endpoint).netloc}/models"

   credential = DefaultAzureCredential(exclude_environment_credential=True,
                                        exclude_managed_identity_credential=True,
                                        exclude_interactive_browser_credential=False)

   chat = ChatCompletionsClient(
            endpoint=inference_endpoint,
            credential=credential,
            credential_scopes=["https://ai.azure.com/.default"])
    ```

    **C#**

    ```csharp
   // Get a chat client
   DefaultAzureCredentialOptions options = new()More actions
           { ExcludeEnvironmentCredential = true,
            ExcludeManagedIdentityCredential = true };
   var projectClient = new AIProjectClient(
            new Uri(project_connection),
            new DefaultAzureCredential(options));
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Catatan**: Kode ini menggunakan klien proyek Azure AI Foundry untuk membuat koneksi aman ke titik akhir layanan Inferensi Model Azure AI default yang terkait dengan proyek Anda. Anda juga dapat terhubung *langsung* ke titik akhir dengan menggunakan SDK Inferensi Model Azure AI, menentukan URI titik akhir yang ditampilkan untuk koneksi layanan di portal Azure AI Foundry atau di halaman sumber daya Layanan Azure AI yang sesuai di portal Azure, dan menggunakan kunci autentikasi atau token kredensial Entra. Untuk informasi selengkapnya tentang menyambungkan ke layanan Inferensi Model Azure AI, lihat [API Inferensi Model Azure AI](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Temukan permintaan **Inisialisasi perintah dengan pesan sistem**, dan tambahkan kode berikut untuk menginisialisasi kumpulan pesan dengan perintah sistem.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Perhatikan bahwa kode menyertakan perulangan untuk memungkinkan pengguna memasukkan perintah hingga mereka memasukkan "berhenti". Kemudian di bagian perulangan, temukan komentar **Dapatkan penyelesaian obrolan** dan tambahkan kode berikut untuk menambahkan input pengguna ke perintah, mengambil penyelesaian dari model Anda, dan menambahkan penyelesaian ke perintah (sehingga Anda mempertahankan riwayat obrolan untuk perulangan di masa mendatang):

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode.

### Masuk ke Azure dan jalankan aplikasi.

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan aplikasinya:

    ```
   az login
    ```

    **<font color="red">Anda harus masuk ke Azure - meskipun sesi cloud shell sudah diautentikasi.</font>**

    > **Catatan**: Dalam sebagian besar skenario, hanya menggunakan *login* az sudah cukup. Namun, jika Anda memiliki langganan di berbagai penyewa, Anda mungkin perlu menentukan penyewa dengan menggunakan *parameter --penyewa* . Lihat [Masuk ke Azure secara interaktif menggunakan Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively) untuk detailnya.
    
1. Saat diperintahkan, ikuti instruksi untuk membuka halaman masuk di tab baru dan masukkan kode autentikasi yang diberikan dan kredensial Azure Anda. Kemudian selesaikan proses masuk di baris perintah, pilih langganan yang berisi pusat penyimpanan Azure AI Foundry jika diperintahkan.
1. Setelah Anda masuk, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

    > **Tips**: Jika kesalahan kompilasi terjadi karena .NET versi 9.0 tidak diinstal, gunakan perintah `dotnet --version` untuk menentukan versi .NET yang diinstal di lingkungan Anda, lalu edit file **chat_app.csproj** di folder kode untuk memperbarui pengaturan **TargetFramework** yang sesuai.

1. Saat diminta, masukkan pertanyaan, seperti `What is the fastest animal on Earth?` dan tinjau respons dari model AI generatif Anda.
1. Coba beberapa pertanyaan tindak lanjut, seperti `Where can I see one?` atau `Are they endangered?`. Percakapan harus dilanjutkan, menggunakan riwayat obrolan sebagai konteks untuk setiap perulangan.
1. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

> **Tips**: Jika aplikasi gagal karena batas rate terlampaui. Tunggu beberapa detik dan coba lagi. Jika tidak ada cukup kuota yang tersedia di langganan Anda, model mungkin tidak dapat merespons.

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi klien untuk model AI generatif yang Anda sebarkan dalam proyek Azure AI Foundry.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Buka [portal Azure](https://portal.azure.com) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
