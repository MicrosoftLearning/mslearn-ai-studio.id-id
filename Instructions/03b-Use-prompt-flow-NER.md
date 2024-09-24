---
lab:
  title: Menggunakan alur perintah untuk Pengenalan Entitas Bernama (NER) di Azure AI Studio
---

# Menggunakan alur perintah untuk Pengenalan Entitas Bernama (NER) di Azure AI Studio

Mengekstrak informasi berharga dari teks dikenal sebagai Pengenal Entitas Bernama (NER). Entitas adalah kata kunci yang menarik bagi Anda dalam teks tertentu.

![Ekstraksi entitas](./media/get-started-prompt-flow-use-case.gif)

Model Bahasa Besar (LLM) dapat digunakan untuk melakukan NER. Untuk membuat aplikasi yang mengambil teks sebagai entitas input dan output, Anda dapat membuat alur yang menggunakan simpul LLM dengan alur prompt.

Dalam latihan ini, Anda akan menggunakan alur prompt Azure AI Studio untuk membuat aplikasi LLM yang mengharapkan jenis entitas dan teks sebagai input. Ini memanggil model GPT dari Azure OpenAI melalui simpul LLM untuk mengekstrak entitas yang diperlukan dari teks tertentu, membersihkan hasil dan menghasilkan entitas yang diekstrak.

![Gambaran umum latihan](./media/get-started-lab.png)

Anda harus terlebih dahulu membuat proyek di Azure AI Studio untuk membuat sumber daya Azure yang diperlukan. Kemudian, Anda dapat menyebarkan model GPT dengan layanan Azure OpenAI. Setelah Anda memiliki sumber daya yang diperlukan, Anda dapat membuat alur. Terakhir, Anda akan menjalankan alur untuk mengujinya dan melihat output sampel.

## Membuat proyek di Azure AI Studio

Anda mulai dengan membuat proyek Azure AI Studio dan Azure AI Hub untuk mendukungnya.

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Pilih halaman **Buat** , lalu pilih **+ Proyek baru**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Azure Hub**: *Buat sumber daya dengan pengaturan berikut:*
        - **Nama AI Hub**: *Nama unik*
        - **Langganan**: *Langganan Azure Anda*
        - **Grup sumber daya**: *Grup sumber daya baru*
        - **Lokasi**: *Buat **pilihan acak** dari salah satu wilayah berikut*\*
        - Australia Timur
        - Kanada Timur
        - AS Timur
        - AS Timur 2
        - Prancis Tengah
        - Jepang Timur
        - AS Tengah Bagian Utara
        - Swedia Tengah
        - Swiss Utara
        - UK Selatan

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Tinjau konfigurasi Anda dan buat proyek Anda.
1. Tunggu 5-10 menit hingga proyek Anda dibuat.

## Sebarkan model GPT

Untuk menggunakan model LLM dalam alur perintah, Anda perlu menyebarkan model terlebih dahulu. Azure AI Studio memungkinkan Anda menyebarkan model OpenAI yang dapat Anda gunakan dalam alur Anda.

1. Di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Penyebaran** .
1. Pada Azure OpenAI Studio, navigasikan ke halaman **Penyebaran** .
1. Buat penyebaran model **gpt-35-turbo** baru dengan pengaturan berikut:
    - **Model:**: `gpt-35-turbo`
    - **Versi model**: *Biarkan nilai default*
    - **Nama penyebaran**: `gpt-35-turbo`
    - Atur opsi **Tingkat Lanjut** untuk menggunakan filter konten default dan untuk membatasi token per menit (TPM) ke **5K**.

Setelah model LLM disebarkan, Anda dapat membuat alur di Azure AI Studio yang memanggil model yang disebarkan.

## Membuat dan menjalankan alur di Azure AI Studio

Sekarang setelah Anda memiliki semua sumber daya yang diperlukan yang disediakan, Anda dapat membuat alur.

### Buat alur baru

Untuk membuat alur baru dengan templat, Anda dapat memilih salah satu jenis alur yang ingin Anda kembangkan.

1. Di panel navigasi di sebelah kiri, di bawah **Alat**, pilih **Alur perintah**.
1. Pilih **+ Buat** untuk membuat alur baru.
1. Buat **Alur standar** baru dan masukkan `entity-recognition` sebagai nama folder.

Aliran standar dengan satu input, dua simpul, dan satu output dibuat untuk Anda. Anda akan memperbarui alur untuk mengambil dua input, mengekstrak entitas, membersihkan output dari simpul LLM, dan mengembalikan entitas sebagai output.

### Memulai runtime otomatis

Untuk menguji alur, Anda memerlukan komputasi. Komputasi yang diperlukan tersedia untuk Anda melalui runtime.

1. Setelah membuat alur baru yang Anda beri nama `entity-recognition`, alur akan terbuka di studio.
1. Pilih bidang **Pilih runtime** dari bilah atas.
1. Di daftar **Runtime otomatis**, pilih **Mulai** untuk memulai runtime otomatis.
1. Tunggu hingga runtime dimulai.

### Mengonfigurasi input

Alur yang akan Anda buat akan mengambil dua input: teks dan jenis entitas yang ingin Anda ekstrak dari teks.

1. Di bawah **Input**, satu input dikonfigurasi bernama `topic` jenis `string`. Ubah input dan pembaruan yang ada dengan pengaturan berikut:
    - **Nama**: `entity_type`
    - **Jenis**: `string`
    - **Nilai**: `job title`
1. Pilih **Tambahkan input**.
1. Konfigurasikan input kedua untuk memiliki pengaturan berikut:
    - **Nama**: `text`
    - **Jenis**: `string`
    - **Nilai**: `The software engineer is working on a new update for the application.`

### Mengonfigurasi simpul LLM

Alur standar sudah mencakup simpul yang menggunakan alat LLM. Anda dapat menemukan simpul dalam gambaran umum alur Anda. Perintah default meminta lelucon. Anda akan memperbarui simpul LLM untuk mengekstrak entitas berdasarkan dua input yang ditentukan di bagian sebelumnya.

1. Navigasi ke **simpul LLM** bernama `joke`.
1. Ganti nama dengan `NER_LLM`
1. Untuk **Koneksi**, pilih `Default_AzureOpenAI` koneksi.
1. Untuk **nama_penyebaran**, pilih model yang `gpt-35-turbo` Anda sebarkan.
1. Ganti bidang perintah dengan kode berikut:

   ```yml
   {% raw %}
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
   {% endraw %}
   ```

1. Pilih **Validasi dan uraikan input**.
1. Dalam simpul LLM, di bagian **Input** , konfigurasikan hal berikut:
    - Untuk `entity_type`, memilih nilai `${inputs.entity_type}`.
    - Untuk `text`memilih nilai `${inputs.text}`.

Simpul LLM Anda sekarang akan mengambil jenis entitas dan teks sebagai input, sertakan dalam perintah yang Anda tentukan dan kirim permintaan ke model yang Anda sebarkan.

### Mengofigurasi simpul Python

Untuk mengekstrak hanya informasi kunci dari hasil model, Anda dapat menggunakan alat Python untuk membersihkan output simpul LLM.

1. Navigasikan ke simpul Python bernama `echo`.
1. Ganti nama dengan `cleansing`.
1. Ganti kode  dengan kode berikut:

   ```python
   from typing import List
   from promptflow import tool
    
    
   @tool
   def cleansing(entities_str: str) -> List[str]:
       # Split, remove leading and trailing spaces/tabs/dots
       parts = entities_str.split(",")
       cleaned_parts = [part.strip(" \t.\"") for part in parts]
       entities = [part for part in cleaned_parts if len(part) > 0]
       return entities
    
   ```

1. Pilih **Validasi dan uraikan input**.
1. Dalam simpul Python, di bagian **Input** , atur nilai `entities_str` ke `${NER_LLM.output}`.

### Mengonfigurasi output

Terakhir, Anda dapat mengonfigurasi output dari seluruh alur. Anda hanya menginginkan satu output ke alur Anda, yang seharusnya menjadi entitas yang diekstrak.

1. Navigasikan ke alur **Output**.
1. Untuk **Nama**, masukkan `entities`.
1. Untuk **Nilai**, pilih `${cleansing.output}`.

### Jalankan alur

Sekarang setelah Anda mengembangkan alur, Anda dapat menjalankannya untuk mengujinya. Karena Anda telah menambahkan nilai default ke input, Anda dapat dengan mudah menguji alur di studio.

1. Pilih **Jalankan** untuk menguji alur.
1. Tunggu hingga sinkronisasi selesai.
1. Pilih **Tinjau-output**. Pop-up akan muncul yang menunjukkan output untuk input default. Secara opsional, Anda juga dapat memeriksa log.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
