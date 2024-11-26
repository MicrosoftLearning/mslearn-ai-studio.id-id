---
lab:
  title: Menggunakan alur perintah untuk Pengenalan Entitas Bernama (NER) di portal Azure AI Foundry
---

# Menggunakan alur perintah untuk Pengenalan Entitas Bernama (NER) di portal Azure AI Foundry

Mengekstrak informasi berharga dari teks dikenal sebagai Pengenal Entitas Bernama (NER). Entitas adalah kata kunci yang menarik bagi Anda dalam teks tertentu.

![Ekstraksi entitas](./media/get-started-prompt-flow-use-case.gif)

Model Bahasa Besar (LLM) dapat digunakan untuk melakukan NER. Untuk membuat aplikasi yang mengambil teks sebagai entitas input dan output, Anda dapat membuat alur yang menggunakan simpul LLM dengan alur prompt.

Dalam latihan ini, Anda akan menggunakan alur perintah Azure AI Foundry untuk membuat aplikasi LLM yang mengharapkan jenis entitas dan teks sebagai input. Ini memanggil model GPT dari Azure OpenAI melalui simpul LLM untuk mengekstrak entitas yang diperlukan dari teks tertentu, membersihkan hasil dan menghasilkan entitas yang diekstrak.

![Gambaran umum latihan](./media/get-started-lab.png)

Anda harus terlebih dahulu membuat proyek di portal Azure AI Foundry untuk membuat sumber daya Azure yang diperlukan. Kemudian, Anda dapat menyebarkan model GPT dengan layanan Azure OpenAI. Setelah Anda memiliki sumber daya yang diperlukan, Anda dapat membuat alur. Terakhir, Anda akan menjalankan alur untuk mengujinya dan melihat output sampel.

## Membuat proyek di portal Azure AI Foundry

Anda mulai dengan membuat proyek Azure AI Foundry dan Azure AI Hub untuk mendukungnya.

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, Anda bisa melihat semua sumber daya Azure yang akan dibuat secara otomatis dengan proyek Anda, atau Anda bisa mengkustomisasi pengaturan berikut dengan memilih **Sesuaikan** sebelum memilih **Buat**:

    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Hub**: *Buat proyek baru dengan pengaturan berikut:*
    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya baru*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-35-turbo** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Sambungkan Layanan Azure AI atau Azure OpenAI**: (Baru) *Autofills dengan nama hub yang Anda pilih*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum di pembantu lokasi mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Jika Anda memilih **Sesuaikan**, pilih **Berikutnya** dan tinjau konfigurasi Anda.
1. Pilih **Buat** dan tunggu hingga prosesnya selesai.

## Sebarkan model GPT

Untuk menggunakan model LLM dalam alur perintah, Anda perlu menyebarkan model terlebih dahulu. Azure AI Foundry memungkinkan Anda menerapkan model OpenAI yang dapat Anda gunakan dalam alur Anda.

1. Di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Buat penyebaran baru model **gpt-35-turbo** dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penerapan:
   
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan
   
Setelah model LLM diterapkan, Anda dapat membuat alur di Azure AI Foundry yang memanggil model yang telah diterapkan.

## Membuat dan menjalankan alur di portal Azure AI Foundry

Sekarang setelah Anda memiliki semua sumber daya yang diperlukan yang disediakan, Anda dapat membuat alur.

### Buat alur baru

Untuk membuat alur baru dengan templat, Anda dapat memilih salah satu jenis alur yang ingin Anda kembangkan.

1. Di panel navigasi di sebelah kiri, di bawah **Membangun dan menyesuaikan**, pilih **Alur prompt**.
1. Pilih **+ Buat** untuk membuat alur baru.
1. Buat **Alur standar** baru dan masukkan `entity-recognition` sebagai nama folder.

<details>  
    <summary><b>Tips pemecahan masalah</b>: Kesalahan izin</summary>
    <p>Jika Anda menerima kesalahan izin saat membuat alur perintah baru, coba langkah berikut untuk memecahkan masalah:</p>
    <ul>
        <li>Di portal Azure, pilih Penjelajah Sumber Daya dari Semua Layanan.</li>
        <li>Pada tab Identitas di bawah Manajemen Sumber Daya, konfirmasikan bahwa itu adalah identitas terkelola yang ditetapkan sistem.</li>
        <li>Lakukan navigasi ke Akun Penyimpanan. Pada halaman IAM, tambahkan penetapan peran <em>Pembaca data blob penyimpanan</em>.</li>
        <li>Di bawah <strong>Tetapkan akses ke</strong>, pilih <strong>Identitas Terkelola</strong>, <strong>+ Pilih anggota</strong>, dan pilih <strong>Semua identitas terkelola yang ditetapkan sistem</strong>.</li>
        <li>Tinjau dan tetapkan untuk menyimpan pengaturan baru dan coba lagi langkah sebelumnya.</li>
    </ul>
</details>

Aliran standar dengan satu input, dua simpul, dan satu output dibuat untuk Anda. Anda akan memperbarui alur untuk mengambil dua input, mengekstrak entitas, membersihkan output dari simpul LLM, dan mengembalikan entitas sebagai output.

### Memulai runtime otomatis

Untuk menguji alur, Anda memerlukan komputasi. Komputasi yang diperlukan tersedia untuk Anda melalui runtime.

1. Setelah membuat alur baru yang Anda beri nama `entity-recognition`, alur akan terbuka di studio.
1. Pilih **Mulai sesi komputasi** dari bilah atas.
1. Sesi komputasi akan memakan waktu 1-3 menit untuk memulai.

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
1. Untuk **Koneksi**, pilih koneksi yang dibuat untuk Anda saat Anda membuat hub AI.
1. Untuk **nama_penyebaran**, pilih model yang `gpt-35-turbo` Anda sebarkan.
1. Ganti bidang perintah dengan kode berikut:

   ```yml
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
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

Setelah selesai menjelajahi portal Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
