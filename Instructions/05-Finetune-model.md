---
lab:
  title: Menyempurnakan model bahasa untuk penyelesaian obrolan di Azure AI Studio
---

# Menyempurnakan model bahasa untuk penyelesaian obrolan di Azure AI Studio

Dalam latihan ini, Anda akan menyempurnakan model bahasa dengan Azure AI Studio yang ingin Anda gunakan untuk skenario salinan kustom.

Latihan ini akan memakan waktu sekitar **45** menit.

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
        - AS Timur 2
        - AS Tengah Bagian Utara
        - Swedia Tengah
        - Swiss Utara
    - **Menyambungkan Azure AI Services atau Azure OpenAI**: *Membuat koneksi baru*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda. Pelajari lebih lanjut tentang [ketersediaan model per wilayah](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Tinjau konfigurasi Anda dan buat proyek Anda.
1. Tunggu proyek Anda dibuat.

## Menyempurnakan model GPT-3.5

Sebelum Anda dapat menyempurnakan model, Anda memerlukan himpunan data.

1. Simpan himpunan data pelatihan sebagai file JSONL secara lokal: https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Navigasikan ke halaman **Penyempurnaan** di bawah bagian **Alat** , menggunakan menu di sebelah kiri.
1. Pilih tombol untuk menambahkan model penyempurnaan baru, pilih `gpt-35-turbo` model, dan pilih **Konfirmasi**.
1. **Sempurnakan** model menggunakan konfigurasi berikut:
    - **Versi model**: *Pilih versi default*
    - **Akhiran model**: `ft-travel`
    - **Sambungan Azure OpenAI**: *Pilih koneksi default yang dibuat saat Anda membuat hub*
    - **Data pelatihan**: Mengunggah file
    - **Unggah file**: Pilih file JSONL yang Anda unduh di langkah sebelumnya.

    > **Tips**: Anda tidak perlu menunggu pemrosesan data selesai untuk melanjutkan ke langkah berikutnya.

    - **Data validasi**: Tidak ada
    - **Parameter tugas**: *Pertahankan pengaturan default*
1. Penyempurnaan akan dimulai dan mungkin perlu waktu untuk menyelesaikannya.

> **Catatan**: Penyempurnaan dan penyebaran dapat memakan waktu, jadi Anda mungkin perlu memeriksa kembali secara berkala untuk menyelesaikan langkah berikutnya.

## Menyebarkan model yang disempurnakan

Saat penyempurnaan telah berhasil diselesaikan, Anda dapat menyebarkan model.

1. Pilih model yang disempurnakan. Pilih tab **Metrik** dan jelajahi metrik penyempurnaan.
1. Sebarkan model yang disempurnakan dengan konfigurasi berikut:
    - **Nama penyebaran**: *Nama unik untuk model Anda, Anda dapat menggunakan default*
    - **Tipe penyebaran**: Standar
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default

## Menguji model yang disempurnakan

Setelah menyebarkan model yang disempurnakan, Anda dapat menguji model seperti Anda dapat menguji model lain yang disebarkan.

1. Saat penyebaran siap, navigasikan ke model yang disempurnakan dan pilih **Buka di taman bermain**.
1. Di jendela obrolan, masukkan kueri `What can you do?` Pemberitahuan bahwa meskipun Anda tidak menentukan pesan sistem untuk menginstruksikan model Anda untuk menjawab pertanyaan terkait perjalanan, model sudah memahami apa yang harus difokuskannya.
1. Coba dengan kueri lain seperti `Where should I go on holiday for my 30th birthday?`

## Menghapus sumber daya Azure

Setelah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

- Navigasikan ke [portal Microsoft Azure](https://portal.azure.com) di `https://portal.azure.com`.
- Di portal Microsoft Azure, pada halaman **Beranda**, pilih **Grup sumber daya**.
- Pilih grup sumber daya yang telah Anda buat untuk latihan ini.
- Di bagian atas halaman **Gambaran Umum** untuk grup sumber daya, pilih **Hapus grup sumber daya**.
- Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih **Hapus**.
