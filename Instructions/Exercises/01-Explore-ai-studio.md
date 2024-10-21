---
lab:
  title: Menjelajahi Azure AI Studio
---

# Menjelajahi Azure AI Studio

Dalam latihan ini, Anda menggunakan Azure AI Studio untuk membuat proyek dan menjelajahi model AI generatif.

> Untuk menyelesaikan latihan ini, langganan Azure Anda harus disetujui untuk akses ke layanan Azure OpenAI. Isi [formulir pendaftaran](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) untuk meminta akses ke model Azure OpenAI.

Latihan ini memakan waktu sekitar **30** menit.

## Buka Azure AI Studio

Mari kita mulai dengan menjelajahi Azure AI Studio.

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda. Beranda Azure AI Studio terlihat mirip dengan gambar berikut:

    ![Cuplikan layar Azure AI Studio.](./media/azure-ai-studio-home.png)

1. Tinjau informasi di beranda dan lihat setiap tab, catat opsi untuk menjelajahi model dan kemampuan, membuat proyek, dan mengelola sumber daya.

## Membuat Azure AI hub

Anda memerlukan hub Azure AI di langganan Azure Anda untuk menghosting proyek. Anda dapat membuat sumber daya ini saat membuat proyek, atau menyediakannya sebelumnya (yang akan kita lakukan dalam latihan ini).

1. Di bagian **Manajemen** , pilih **Semua hub**, lalu pilih **+ Hub baru**. Buat proyek baru dengan pengaturan berikut:
    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya dengan nama unik*.
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
    - **Menyambungkan Azure AI Services atau Azure OpenAI**: *Pilih untuk membuat Layanan AI baru atau gunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

    Setelah hub Azure AI dibuat, hub tersebut akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar detail hub Azure AI di Azure AI Studio.](./media/azure-ai-resource.png)

1. Buka tab browser baru (biarkan tab Azure AI Studio terbuka) dan telusuri ke portal Azure di [https://portal.azure.com](https://portal.azure.com?azure-portal=true), masuk dengan kredensial Azure Anda jika diminta.
1. Telusuri ke grup sumber daya tempat Anda membuat hub Azure AI, dan lihat sumber daya Azure yang telah dibuat.

    ![Cuplikan layar hub Azure AI dan sumber daya terkait di portal Azure.](./media/azure-portal.png)

1. Kembali ke tab browser Azure AI Studio.
1. Tampilkan setiap halaman di panel di sisi kiri halaman untuk hub Azure AI Anda, dan perhatikan artefak yang bisa Anda buat dan kelola. Pada halaman **Koneksi** , amati bahwa koneksi ke Layanan Azure OpenAI dan AI telah dibuat.

## Membuat proyek

Hub Azure AI menyediakan ruang kerja kolaboratif tempat Anda dapat menentukan satu atau beberapa *proyek*. Mari kita buat proyek di hub Azure AI Anda.

1. Di Azure AI Studio, pastikan Anda berada di hub yang baru saja Anda buat (Anda dapat memverifikasi lokasi Anda dengan memeriksa jalur di bagian atas layar).
1. Navigasi ke **Semua proyek** menggunakan menu di sebelah kiri.
1. Pilih **+ Proyek baru**.
1. Di wizard **Buat proyek baru** buat proyek dengan pengaturan berikut:
    - **Hub saat ini**: *Hub AI Anda*
    - **Nama proyek**: *Nama unik untuk proyek Anda*
1. Tunggu proyek Anda dibuat. Halaman ini akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar halaman detail proyek di portal Azure AI Studio.](./media/azure-ai-project.png)

1. Tampilkan halaman di panel di sisi kiri, perluas setiap bagian, dan perhatikan tugas yang bisa Anda lakukan dan sumber daya yang bisa Anda kelola dalam proyek.

## Menyebarkan dan menguji model

Anda dapat menggunakan proyek untuk membuat solusi AI yang kompleks berdasarkan model AI generatif. Eksplorasi penuh dari semua opsi pengembangan yang tersedia di Azure AI Studio berada di luar cakupan latihan ini, tetapi kami akan menjelajahi beberapa cara dasar di mana Anda dapat bekerja dengan model dalam proyek.

1. Di panel sebelah kiri untuk proyek Anda, di bagian **Komponen** pilih halaman **Penyebaran** .
1. Pada halaman **Penyebaran** , di tab **Penyebaran model** pilih **+ Buat penyebaran**.
1. Cari model **gpt-35-turbo** dari daftar, pilih dan konfirmasi.
1. Sebarkan model dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Versi model**: *Pilih versi default*
    - **Tipe penyebaran**: Standar
    - **Sumber daya Azure OpenAI yang tersambung**: *Pilih koneksi default yang dibuat saat Anda membuat hub*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: Default

    > **Catatan**: Mengurangi TPM membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 5.000 TPM cukup untuk data yang digunakan dalam latihan ini.

1. Setelah model disebarkan, di halaman gambaran umum penyebaran, pilih **Buka di playground**.
1. Di halaman **obrolan playground** pastikan bahwa penyebaran model Anda dipilih di bagian **Penyebaran** .
1. Di jendela obrolan, masukkan kueri seperti *Apa itu AI?* dan lihat responsnya:

    ![Cuplikan layar playground Obrolan di Azure AI Studio.](./media/playground.png)

## Penghapusan

Setelah selesai menjelajahi Azure AI Studio, Anda harus menghapus sumber daya yang telah Anda buat untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com?azure-portal=true) di tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
