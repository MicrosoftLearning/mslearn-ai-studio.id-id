---
lab:
  title: Mengembangkan aplikasi AI generatif multimodal
  description: 'Pelajari cara menggunakan Azure AI Foundry untuk membuat aplikasi AI generatif yang mendukung input teks, gambar, dan audio.'
---

# Mengembangkan aplikasi AI generatif multimodal

Dalam latihan ini, Anda menggunakan model AI generatif *Phi-4-multimodal-instruct* untuk menghasilkan respons terhadap perintah yang mencakup teks, gambar, dan audio. Anda akan mengembangkan aplikasi yang memberikan bantuan AI dengan produk baru di toko kelontong dengan menggunakan Azure AI Foundry dan layanan Inferensi Model Azure AI.

Latihan ini memakan waktu sekitar **30** menit.

> **Catatan**: Latihan ini didasarkan pada SDK pra-rilis, yang mungkin dapat berubah. Jika perlu, kami telah menggunakan versi paket tertentu; yang mungkin tidak mencerminkan versi terbaru yang tersedia.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/ai-foundry-home.png)

2. Di beranda, pilih **+ Buat proyek**.
3. Di wizard **Buat proyek**, masukkan nama yang valid untuk proyek Anda dan jika hub yang telah ada disarankan, pilih opsi untuk membuat yang baru. Kemudian tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung hub dan proyek Anda.
4. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub**: *Nama yang valid untuk hub Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: Pilih salah satu wilayah berikut\*:
        - US Timur
        - US Timur 2
        - US Tengah Utara
        - US Tengah Selatan
        - Swedia Tengah
        - US Barat
        - US Barat 3
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Pada saat penulisan, model Microsoft *Phi-4-multimodal-instruct* yang akan kita gunakan dalam latihan ini tersedia di wilayah ini. Anda dapat memeriksa ketersediaan regional terbaru untuk model tertentu dalam [dokumentasi Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

5. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
6. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](./media/ai-foundry-project.png)

## Terapkan model

Sekarang Anda siap untuk menyebarkan model *Phi-4-multimodal-instruct* untuk mendukung perintah multimodal.

1. Di toolbar di kanan atas halaman proyek Azure AI Foundry Anda, gunakan ikon **Fitur pratinjau** (**&#9215;**) untuk memastikan bahwa fitur **Sebarkan model ke layanan inferensi model Azure AI** diaktifkan. Fitur ini memastikan penyebaran model Anda tersedia untuk layanan Inferensi Azure AI, yang akan Anda gunakan dalam kode aplikasi Anda.
2. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
3. Pada halaman **Model + titik akhir** , di tab **Penyebaran model** di menu **+ Sebarkan model** pilih **Sebarkan model dasar**.
4. Cari model **Phi-4-multimodal-instruct** dalam daftar, lalu pilih dan konfirmasikan.
5. Setujui perjanjian lisensi jika diminta, lalu sebarkan model dengan pengaturan berikut dengan memilih **Sesuaikan** dalam detail penyebaran:
    - **Nama penyebaran**: *Nama yang valid untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar Global
    - **Detail penyebaran**: *Gunakan pengaturan default*
6. Tunggu hingga status penyediaan penyebaran menjadi **Selesai**.

## Membuat aplikasi klien

Setelah menyebarkan model, Anda dapat menggunakan penyebaran dalam aplikasi klien.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
2. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
3. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.

    Tutup pemberitahuan selamat datang apa pun untuk melihat halaman beranda portal Azure.

1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell*** dengan tidak ada penyimpanan pada langganan Anda.

    Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure. Anda dapat mengubah ukuran atau memaksimalkan panel ini untuk mempermudah pekerjaan.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

5. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    **<font color="red">Pastikan Anda telah beralih ke versi klasik cloud shell sebelum melanjutkan.</font>**

1. Di panel cloud shell, masukkan perintah berikut untuk mengkloning repo GitHub yang berisi file kode untuk latihan ini (ketik perintah, atau salin ke clipboard lalu klik kanan di baris perintah dan tempel sebagai teks biasa):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tips**: Saat Anda menempelkan perintah ke cloudshell, ouputnya mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

7. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode aplikasi obrolan:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka yang akan Anda gunakan:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
    ```

9. Masukkan perintah berikut untuk mengedit file konfigurasi yang telah disediakan:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    File dibuka dalam editor kode.

10. Dalam file kode, ganti penanda **your_project_connection_string** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry), dan penanda **your_model_deployment** dengan nama yang Anda tetapkan ke penyebaran model Phi-4-multimodal-instruct Anda.
11. Setelah Anda mengganti tempat penampung, di editor kode,gunakan perintah **CTRL+S** atau **Klik kanan > Simpan** untuk menyimpan perubahan Anda dan kemudian gunakan perintah **CTRL+Q** atau **Klik kanan > Keluar** untuk menutup editor kode sambil tetap membuka baris perintah cloud shell.

### Menulis kode untuk menyambungkan ke proyek Anda dan mendapatkan klien obrolan untuk model Anda

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

2. Dalam file kode, perhatikan pernyataan yang sudah ada yang telah ditambahkan di bagian atas file untuk mengimpor namespace SDK yang diperlukan. Kemudian, di bawah komentar **Tambahkan referensi**, tambahkan kode berikut untuk mereferensikan namespace di pustaka yang Anda instal sebelumnya:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. Dalam fungsi **utama**, di bawah komentar **Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tentukan dalam file konfigurasi.
4. Di bawah komentar **Inisialisasi klien proyek**, tambahkan kode berikut untuk menghubungkan proyek Azure AI Foundry Anda dengan menggunakan kredensial Azure yang Anda gunakan untuk masuk saat ini:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. Di bawah komentar **Dapatkan klien obrolan**, tambahkan kode berikut untuk membuat objek klien untuk mengobrol dengan model:

    **Python**

    ```
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Menulis kode untuk menggunakan perintah berbasis teks

1. Perhatikan bahwa kode menyertakan perulangan untuk memungkinkan pengguna memasukkan perintah hingga mereka memasukkan "berhenti". Selanjutnya, di bagian perulangan, di bawah komentar **Dapatkan respons terhadap input teks**, tambahkan kode berikut untuk mengirimkan prompt berbasis teks dan mengambil respons dari model Anda:

    **Python**

    ```python
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode - tetapi jangan ditutup dulu.

3. Di panel baris perintah cloud shell di bawah editor kode, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Saat diminta, masukkan `1` untuk menggunakan perintah berbasis teks lalu masukkan perintah `I want to make an apple pie. What kind of apple should I use?`
5. Tinjau responsnya. Kemudian masukkan `quit` untuk keluar dari program.

### Menulis kode untuk menggunakan perintah berbasis gambar

1. Di editor kode untuk file **chat-app.py**, di bagian perulangan, di bawah komentar **Dapatkan respons terhadap input gambar**, tambahkan kode berikut untuk mengirimkan perintah yang menyertakan gambar berikut:

    ![Foto sebuah jeruk.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode - tetapi jangan ditutup dulu.

3. Di panel baris perintah cloud shell di bawah editor kode, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Saat diminta, masukkan `2` untuk menggunakan perintah berbasis gambar lalu masukkan prompt `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?`
5. Tinjau responsnya. Kemudian masukkan `quit` untuk keluar dari program.

### Menulis kode untuk menggunakan prompt berbasis audio

1. Di editor kode untuk file **chat-app.py**, di bagian perulangan, di bawah komentar **Dapatkan respons terhadap input audio**, tambahkan kode berikut untuk mengirimkan perintah yang menyertakan audio berikut:

    <video controls src="./media/manzanas.mp4" title="Waktunya adalah 2:15" width="150"></video>

    **Python**

    ```python
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```


2. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode. Anda juga dapat menutup editor kode (**CTRL+Q**) jika mau.

3. Di panel baris perintah cloud shell di bawah editor kode, masukkan perintah berikut untuk menjalankan aplikasi:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Saat diminta, masukkan `3` untuk menggunakan perintah berbasis audio lalu masukkan perintah `What is this customer saying in English?`
5. Tinjau responsnya.
6. Anda dapat terus menjalankan aplikasi, memilih jenis perintah yang berbeda dan mencoba permintaan yang berbeda. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

    Jika Anda punya waktu, Anda dapat memodifikasi kode untuk menggunakan permintaan sistem yang berbeda dan file gambar dan audio yang dapat diakses internet Anda sendiri.

    > **Catatan**: Dalam aplikasi sederhana ini, kami belum menerapkan logika untuk mempertahankan riwayat percakapan; sehingga model akan memperlakukan setiap permintaan sebagai permintaan baru tanpa konteks perintah sebelumnya.

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry dan SDK Azure AI Inference untuk membuat aplikasi klien menggunakan model multimodal untuk menghasilkan respons terhadap teks, gambar, dan audio.

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com) di `https://portal.azure.com` tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
