---
lab:
  title: Menggunakan alur perintah untuk mengelola percakapan di aplikasi obrolan
  description: Pelajari cara menggunakan alur perintah untuk mengelola dialog percakapan dan memastikan bahwa perintah dibuat dan diatur untuk hasil terbaik.
---

# Menggunakan alur perintah untuk mengelola percakapan di aplikasi obrolan

Dalam latihan ini, Anda akan menggunakan alur perintah portal Azure AI Foundry untuk membuat aplikasi obrolan khusus yang menggunakan perintah pengguna dan riwayat obrolan sebagai input, lalu menggunakan model GPT dari Azure OpenAI untuk menghasilkan keluaran.

Latihan ini akan memakan waktu sekitar **30** menit.

## Membuat AI hub dan proyek di portal Azure AI Foundry.

Anda mulai dengan membuat proyek portal Azure AI Foundry dalam hub Azure AI:

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, Anda bisa melihat semua sumber daya Azure yang akan dibuat secara otomatis dengan proyek Anda, atau Anda bisa mengkustomisasi pengaturan berikut dengan memilih **Sesuaikan** sebelum memilih **Buat**:

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

Untuk menggunakan model bahasa dalam alur perintah, Anda perlu menyebarkan model terlebih dahulu. Azure AI Foundry memungkinkan Anda menerapkan model OpenAI yang dapat Anda gunakan dalam alur Anda.

1. Di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Buat penyebaran model **gpt-35-turbo** baru dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > **Catatan**: Jika lokasi sumber daya AI Anda saat ini tidak memiliki kuota yang tersedia untuk model yang ingin Anda terapkan, Anda akan diminta untuk memilih lokasi lain tempat sumber daya AI baru akan dibuat dan tersambung ke proyek Anda.

1. Tunggu hingga aplikasi disebarkan. Saat penyebaran siap, pilih **Buka di playground**.
1. Di jendela obrolan, masukkan kueri `What can you do?`.

    Perhatikan bahwa jawabannya umum karena tidak ada instruksi khusus untuk asisten. Untuk membuatnya terfokus pada tugas, Anda dapat mengubah permintaan sistem.

1. Ubah pesan **Berikan instruksi dan konteks model** ke yang berikut ini:

   ```md
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Pilih **Terapkan perubahan**.
1. Di jendela obrolan, masukkan kueri yang sama seperti sebelumnya: `What can you do?` Perhatikan perubahan respons.

Sekarang setelah Anda bermain-main dengan pesan sistem untuk model GPT yang disebarkan, Anda dapat menyesuaikan aplikasi lebih lanjut dengan bekerja dengan alur prompt.

## Membuat dan menjalankan alur obrolan di portal Azure AI Foundry

Anda dapat membuat alur baru dari templat, atau membuat alur berdasarkan konfigurasi Anda di taman bermain. Karena Anda sudah bereksperimen di taman bermain, Anda akan menggunakan opsi ini untuk membuat alur baru.

<details>  
    <summary><b>Tips pemecahan masalah</b>: Kesalahan izin</summary>
    <p>Jika Anda menerima kesalahan izin saat membuat alur perintah baru, coba langkah berikut untuk memecahkan masalah:</p>
    <ul>
        <li>Di portal Azure, pilih Penjelajah Sumber Daya dari Semua Layanan.</li>
        <li>Pada tab Identitas di bawah Manajemen Sumber Daya, konfirmasikan bahwa itu adalah identitas terkelola yang ditetapkan sistem.</li>
        <li>Lakukan navigasi ke Akun Penyimpanan. Pada halaman IAM, tambahkan penetapan peran <em>Pembaca data blob penyimpanan</em>.</li>
        <li>Di bawah <strong>Tetapkan akses ke</strong>, pilih <strong>Identitas Terkelola</strong>, <strong>+ Pilih anggota</strong>, dan pilih <strong>Semua identitas terkelola yang ditetapkan sistem</strong>, dan pilih sumber daya layanan Azure AI Anda.</li>
        <li>Tinjau dan tetapkan untuk menyimpan pengaturan baru dan coba lagi langkah sebelumnya.</li>
    </ul>
</details>

1. Di **Obrolan playground**, pilih **Alur perintah** dari bilah atas.
1. Masukkan `Travel-Chat` sebagai nama folder.

    Alur obrolan sederhana dibuat untuk Anda. Perhatikan ada dua input (riwayat obrolan dan pertanyaan pengguna), simpul LLM yang akan terhubung dengan model bahasa yang Anda sebarkan, dan output untuk mencerminkan respons dalam obrolan.

    Untuk dapat menguji alur Anda, Anda memerlukan komputasi.

1. Pilih **Mulai sesi komputasi** dari bilah atas.
1. Sesi komputasi akan memakan waktu 1-3 menit untuk memulai.
1. Temukan simpul LLM bernama **obrolan**. Perhatikan bahwa perintah sudah menyertakan permintaan sistem yang Anda tentukan di taman bermain obrolan.

    Anda masih perlu menyambungkan simpul LLM ke model yang Anda sebarkan.

1. Di bagian simpul LLM, untuk **Koneksi**, pilih koneksi yang dibuat untuk Anda saat Anda membuat hub AI.
1. Untuk **Api**, pilih **obrolan**.
1. Untuk **nama_penyebaran**, pilih model **gpt-35-turbo** yang Anda sebarkan.
1. Untuk **format_respons**, pilih **{"type":"text"}**.
1. Tinjau bidang perintah dan pastikan bidang tersebut terlihat seperti berikut ini:

   ```yml
   system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   ```

### Menguji dan menyebarkan alur

Setelah mengembangkan alur, Anda dapat menggunakan jendela obrolan untuk menguji alur.

1. Pastikan sesi komputasi berjalan.
1. Pilih **Simpan**.
1. Pilih **Obrolan** untuk menguji alur.
1. Masukkan kueri: `I have one day in London, what should I do?` dan tinjau output.

    Saat Anda puas dengan perilaku alur yang Anda buat, Anda dapat menyebarkan alur.

1. Pilih **Sebarkan** untuk menyebarkan alur dengan pengaturan berikut:
    - **Pengaturan dasar**:
        - **Titik akhir**: Baru
        - **Nama titik akhir**: *Masukkan nama unik*
        - **Nama penyebaran**: *Masukkan nama yang unik*
        - **Komputer virtual**: Standard_DS3_v2
        - **Jumlah instans**: 3
        - **Inferensi pengumpulan data**: Aktif
    - **Pengaturan tingkat lanjut**:
        - *Menjaga pengaturan default*
1. Di portal Azure AI Foundry, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Aset saya**, pilih halaman **Model + titik akhir**.
1. Perhatikan bahwa secara default **Penerapan model** tercantum, termasuk model bahasa yang digunakan dan alur yang digunakan. Mungkin perlu waktu sebelum penyebaran dicantumkan dan berhasil dibuat.
1. Ketika penyebaran telah berhasil, pilih penyebaran tersebut. Kemudian, pada halaman **Uji** , masukkan perintah `What is there to do in San Francisco?` dan tinjau responsnya.
1. Masukkan perintah `Where else could I go?` dan tinjau responsnya.
1. Lihat halaman **Konsumsi** untuk titik akhir, dan perhatikan bahwa halaman tersebut berisi informasi koneksi dan kode sampel yang dapat Anda gunakan untuk membangun aplikasi klien untuk titik akhir Anda - memungkinkan Anda mengintegrasikan solusi alur perintah ke dalam aplikasi sebagai salinan kustom.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi portal Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
