---
lab:
  title: Membuat salinan kustom dengan alur prompt di Azure AI Studio
---

# Membuat salinan kustom dengan alur prompt di Azure AI Studio

Dalam latihan ini, Anda akan menggunakan alur prompt Azure AI Studio untuk membuat salinan kustom yang menggunakan perintah pengguna dan riwayat obrolan sebagai input, dan menggunakan model GPT dari Azure OpenAI untuk menghasilkan output.

Latihan ini akan memakan waktu sekitar **30** menit.

## Sebelum memulai

Untuk menyelesaikan latihan ini, langganan Azure Anda harus disetujui untuk akses ke layanan Azure OpenAI. Isi [formulir pendaftaran](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) untuk meminta akses ke model Azure OpenAI.

## Membuat hub dan proyek AI di Azure AI Studio

Anda mulai dengan membuat proyek Azure AI Studio dalam hub Azure AI:

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.
1. Pilih halaman **Beranda** lalu pilih **+ Proyek baru**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Hub**: *Buat proyek baru dengan pengaturan berikut:*
        - **Nama hub**: *Nama unik*
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
    - **Menyambungkan Azure AI Services atau Azure OpenAI**: *Membuat koneksi baru*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Tinjau konfigurasi Anda dan buat proyek Anda.
1. Tunggu proyek Anda dibuat.

## Sebarkan model GPT

Untuk menggunakan model bahasa dalam alur perintah, Anda perlu menyebarkan model terlebih dahulu. Azure AI Studio memungkinkan Anda menyebarkan model OpenAI yang dapat Anda gunakan dalam alur Anda.

1. Di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Penyebaran** .
1. Buat penyebaran model **gpt-35-turbo** baru dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Versi model**: *Pilih versi default*
    - **Tipe penyebaran**: Standar
    - **Sumber daya Azure OpenAI yang tersambung**: *Pilih koneksi default*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default
1. Tunggu hingga aplikasi disebarkan. Saat penyebaran siap, pilih **Buka di playground**.
1. Di jendela obrolan, masukkan kueri `What can you do?`.

    Perhatikan bahwa jawabannya umum karena tidak ada instruksi khusus untuk asisten. Untuk membuatnya terfokus pada tugas, Anda dapat mengubah permintaan sistem.

1. Ubah kode **Pesan sistem** ke kode berikut:

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

## Membuat dan menjalankan alur obrolan di Azure AI Studio

Anda dapat membuat alur baru dari templat, atau membuat alur berdasarkan konfigurasi Anda di taman bermain. Karena Anda sudah bereksperimen di taman bermain, Anda akan menggunakan opsi ini untuk membuat alur baru.

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
   {% raw %}
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
   {% endraw %}
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
1. Di Azure AI Studio, di proyek Anda, di panel navigasi di sebelah kiri, di bawah **Komponen**, pilih halaman **Penyebaran** .
1. Perhatikan bahwa secara default **penyebaran model** tercantum, termasuk model bahasa yang Anda sebarkan.
1. Pilih tab **Penyebaran aplikasi** untuk menemukan alur yang Anda sebarkan. Mungkin perlu waktu sebelum penyebaran dicantumkan dan berhasil dibuat.
1. Ketika penyebaran telah berhasil, pilih penyebaran tersebut. Kemudian, pada halaman **Uji** , masukkan perintah `What is there to do in San Francisco?` dan tinjau responsnya.
1. Masukkan perintah `Where else could I go?` dan tinjau responsnya.
1. Lihat halaman **Konsumsi** untuk titik akhir, dan perhatikan bahwa halaman tersebut berisi informasi koneksi dan kode sampel yang dapat Anda gunakan untuk membangun aplikasi klien untuk titik akhir Anda - memungkinkan Anda mengintegrasikan solusi alur perintah ke dalam aplikasi sebagai salinan kustom.

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.