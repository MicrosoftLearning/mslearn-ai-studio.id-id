---
lab:
  title: Membuat aplikasi obrolan AI generatif
  description: Pelajari cara menggunakan Azure AI Foundry SDK untuk membangun aplikasi yang terhubung ke proyek dan obrolan Anda dengan model bahasa.
---

# Membuat aplikasi obrolan AI generatif

Dalam latihan ini, Anda menggunakan Azure AI Foundry SDK untuk membuat aplikasi obrolan sederhana yang terhubung ke proyek dan obrolan dengan model bahasa.

Latihan ini memakan waktu sekitar **40** menit.

> **Catatan**: Latihan ini didasarkan pada SDK pra-rilis, yang mungkin dapat berubah. Jika perlu, kami telah menggunakan versi paket tertentu; yang mungkin tidak mencerminkan versi terbaru yang tersedia.

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
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-4** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru dengan nama yang sesuai (misalnya, `my-ai-services`) atau menggunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Jika batas kuota regional tercapai di akhir latihan, ada kemungkinan Anda perlu membuat sumber daya lain di wilayah lain.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

## Menyebarkan model AI generatif

Sekarang Anda telah siap untuk menyebarkan model bahasa AI generatif untuk mendukung aplikasi obrolan Anda. Dalam contoh ini, Anda akan menggunakan model GPT-4 OpenAI; tetapi prinsip-prinsipnya sama untuk model apa pun.

1. Di toolbar di kanan atas halaman proyek Azure AI Foundry Anda, gunakan ikon **Fitur pratinjau** untuk mengaktifkan fitur **Sebarkan model ke layanan inferensi model Azure AI**. Fitur ini memastikan penyebaran model Anda tersedia untuk layanan Inferensi Azure AI, yang akan Anda gunakan dalam kode aplikasi Anda.
1. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
1. Pada halaman **Model + titik akhir** , di tab **Penyebaran model** di menu **+ Sebarkan model** pilih **Sebarkan model dasar**.
1. Cari model **gpt-4** dari daftar, pilih dan konfirmasi.
1. Terapkan model dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penyeberan:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda - sebagai contoh `gpt-4`*
    - **Tipe penyebaran**: Standar Global
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI yang terhubung**: *Koneksi sumber daya Azure OpenAI Anda*
    - **Batas Tingkat Token per Menit (ribu)**: 5rb (*atau maksimum yang tersedia jika lebih rendah*)
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > **Catatan**: Mengurangi TPM membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 5.000 TPM cukup untuk data yang digunakan dalam latihan ini.

1. Tunggu hingga status penyediaan penyebaran menjadi **Selesai**.

## Membuat aplikasi klien untuk mengobrol dengan model

Sekarang setelah Anda menerapkan model, Anda dapat menggunakan Azure AI Foundry dan SDK inferensi model Azure AI untuk mengembangkan aplikasi yang mengobrol dengannya.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.
1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell***. Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    **<font color="red">Pastikan Anda telah beralih ke versi klasik cloud shell sebelum melanjutkan.</font>**

1. Di panel PowerShell, masukkan perintah berikut untuk mengkloning repositori GitHub untuk latihan ini:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tips**: Saat Anda menempelkan perintah ke cloudshell, ouput mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

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

1. Dalam file kode, ganti tempat penampung **your_project_endpoint** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry), dan **tempat penampung your_model_deployment** dengan nama yang Anda tetapkan ke penerapan model GPT-4 Anda.
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
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Dalam fungsi **utama**, di bawah komentar **Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tentukan dalam file konfigurasi.
1. Temukan komentar **Inisialisasi klien proyek**, tambahkan kode berikut untuk menghubungkan proyek Azure AI Foundry Anda dengan menggunakan kredensial Azure yang Anda gunakan untuk masuk saat ini:

    > **Tips**: Berhati-hatilah untuk mempertahankan tingkat indentasi yang benar untuk kode Anda.

    **Python**

    ```python
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Temukan komentar **Dapatkan klien obrolan**, tambahkan kode berikut untuk membuat objek klien untuk mengobrol dengan model:

    **Python**

    ```python
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Catatan**: Kode ini menggunakan klien proyek Azure AI Foundry untuk membuat koneksi aman ke titik akhir layanan Inferensi Model Azure AI default yang terkait dengan proyek Anda. Anda juga dapat terhubung *langsung* ke titik akhir dengan menggunakan SDK Inferensi Model Azure AI, menentukan URI titik akhir yang ditampilkan untuk koneksi layanan di portal Azure AI Foundry atau di halaman sumber daya Layanan Azure AI yang sesuai di portal Azure, dan menggunakan kunci autentikasi atau token kredensial Entra. Untuk informasi selengkapnya tentang menyambungkan ke layanan Inferensi Model Azure AI, lihat [API Inferensi Model Azure AI](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Temukan permintaan **Inisialisasi perintah dengan pesan sistem**, dan tambahkan kode berikut untuk menginisialisasi kumpulan pesan dengan perintah sistem.

    **Python**

    ```python
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
    var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Perhatikan bahwa kode menyertakan perulangan untuk memungkinkan pengguna memasukkan perintah hingga mereka memasukkan "berhenti". Kemudian di bagian perulangan, temukan komentar **Dapatkan penyelesaian obrolan** dan tambahkan kode berikut untuk menambahkan input pengguna ke perintah, mengambil penyelesaian dari model Anda, dan menambahkan penyelesaian ke perintah (sehingga Anda mempertahankan riwayat obrolan untuk perulangan di masa mendatang):

    **Python**

    ```python
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

### Jalankan aplikasi obrolan tersebut

1. Di panel baris perintah cloud shell di bawah editor kode, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Saat diminta, masukkan pertanyaan, seperti `What is the fastest animal on Earth?` dan tinjau respons dari model AI generatif Anda.
1. Coba beberapa pertanyaan tindak lanjut, seperti `Where can I see one?` atau `Are they endangered?`. Percakapan harus dilanjutkan, menggunakan riwayat obrolan sebagai konteks untuk setiap perulangan.
1. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

## Menggunakan SDK OpenAI

Aplikasi klien Anda dibangun menggunakan SDK Inferensi Model Azure AI, yang berarti dapat digunakan dengan model apa pun yang disebarkan ke layanan Inferensi Model Azure AI. Model yang Anda sebarkan adalah model GPT OpenAI, yang juga dapat Anda gunakan menggunakan SDK OpenAI.

Mari kita buat beberapa modifikasi kode untuk melihat cara mengimplementasikan aplikasi obrolan menggunakan SDK OpenAI.

1. Di baris perintah cloud shell untuk folder kode Anda (*python* atau *c-sharp*), masukkan perintah berikut untuk menginstal paket yang diperlukan:

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.5
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Catatan**: Versi pra-rilis yang berbeda dari paket Azure.AI.Projects diperlukan sebagai solusi sementara untuk beberapa ketidakcocokan dengan SDK Inferensi Model Azure AI.

1. Jika file kode Anda (*chat-app.py* atau *Program.cs*) belum terbuka, masukkan perintah berikut untuk membukanya di editor kode:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Di bagian atas file kode, tambahkan referensi berikut ini:

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Temukan komentar **Dapatkan klien obrolan**, dan ubah kode yang digunakan untuk membuat objek klien sebagai berikut:

    **Python**

    ```python
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Catatan**: Kode ini menggunakan klien proyek Azure AI Foundry untuk membuat koneksi aman ke titik akhir layanan Azure OpenAI default yang terkait dengan proyek Anda. Anda juga dapat terhubung *langsung* ke titik akhir dengan menggunakan SDK Azure OpenAI, menentukan URI titik akhir yang ditampilkan untuk koneksi layanan di portal Azure AI Foundry atau di halaman sumber daya Azure OpenAI atau AI Services yang sesuai di portal Azure, dan menggunakan kunci autentikasi atau token kredensial Entra. Untuk informasi selengkapnya tentang menyambungkan ke layanan Azure OpenAI, lihat [Bahasa pemrograman yang didukung Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Temukan permintaan **Inisialisasi komentar dengan pesan sistem**, dan ubah kode untuk menginisialisasi kumpulan pesan dengan perintah sistem sebagai berikut:

    **Python**

    ```python
   prompt=[
       {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
   ]
    ```

    **C#**

    ```csharp
    var prompt = new List<ChatMessage>(){
       new SystemChatMessage("You are a helpful AI assistant that answers questions.")
   };
    ```

1. Temukan komentar **Dapatkan penyelesaian obrolan** dan ubah kode untuk menambahkan input pengguna ke perintah, mengambil penyelesaian dari model Anda, dan menambahkan penyelesaian ke perintah sebagai berikut:

    **Python**

    ```python
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
       model=model_deployment,
       messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

    **C#**

    ```csharp
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode.

1. Di panel baris perintah cloud shell di bawah editor kode, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Uji aplikasi dengan mengirimkan pertanyaan seperti sebelumnya. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

    > **Catatan**: SDK Inferensi Model Azure AI dan SDK OpenAI menggunakan kelas dan konstruksi kode serupa, sehingga kode memerlukan perubahan minimal. Anda dapat menggunakan SDK Inferensi Model Azure AI dengan model *apa pun* yang disebarkan ke titik akhir layanan Inferensi Model Azure AI. SDK OpenAI hanya berfungsi dengan model OpenAI, tetapi Anda dapat menggunakannya untuk model yang disebarkan ke titik akhir layanan Inferensi Model Azure AI atau ke titik akhir Azure OpenAI.  

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry, Inferensi Model Azure AI, dan SDK Azure OpenAI untuk membuat aplikasi klien untuk model AI generatif yang Anda gunakan dalam proyek Azure AI Foundry.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com) di `https://portal.azure.com` tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
