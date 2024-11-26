---
lab:
  title: jJelajahi komponen dan alat Azure AI Foundry
---

# jJelajahi komponen dan alat Azure AI Foundry

Dalam latihan ini, Anda menggunakan Azure AI Foundry untuk membuat proyek dan menjelajahi model AI generatif.

Latihan ini memakan waktu sekitar **30** menit.

## Buka portal Azure AI Foundry

Mari kita mulai dengan menjelajahi portal Azure AI Foundry.

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda. Beranda Azure AI Foundry terlihat mirip dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](./media/azure-ai-studio-home.png)

1. Tinjau informasi di beranda dan lihat setiap tab, catat opsi untuk menjelajahi model dan kemampuan, membuat proyek, dan mengelola sumber daya.

## Membuat hub dan proyek Azure AI

Hub Azure AI menyediakan ruang kerja kolaboratif tempat Anda dapat menentukan satu atau beberapa *proyek*. Mari kita buat proyek dan hub Azure AI.

1. Di beranda, pilih **+ Buat proyek**. Di wizard **Buat proyek**, Anda bisa melihat semua sumber daya Azure yang akan dibuat secara otomatis dengan proyek Anda, atau Anda bisa mengkustomisasi pengaturan berikut dengan memilih **Sesuaikan** sebelum memilih **Buat**:
   
    - **Nama hub**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya dengan nama unik*.
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **gpt-35-turbo** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Menyambungkan Azure AI Services atau Azure OpenAI**: *Pilih untuk membuat Layanan AI baru atau gunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Jika Anda memilih **Sesuaikan**, pilih **Berikutnya** dan tinjau konfigurasi Anda.
1. Pilih **Buat** dan tunggu hingga prosesnya selesai.
   
    Setelah hub dan proyek Azure AI dibuat, tampilannya akan terlihat seperti gambar berikut:

    ![Tangkapan layar detail hub Azure AI di portal Azure AI Foundry.](./media/azure-ai-resource.png)

1. Buka tab browser baru (biarkan tab Azure AI Foundry terbuka) dan telusuri ke portal Azure di [https://portal.azure.com](https://portal.azure.com?azure-portal=true), masuk dengan kredensial Azure Anda jika diminta.
1. Telusuri ke grup sumber daya tempat Anda membuat hub Azure AI, dan lihat sumber daya Azure yang telah dibuat.

    ![Cuplikan layar hub Azure AI dan sumber daya terkait di portal Azure.](./media/azure-portal.png)

1. Kembali ke tab browser portal Azure AI Foundry.
1. Tampilkan setiap halaman di panel di sisi kiri halaman untuk hub Azure AI Anda, dan perhatikan artefak yang bisa Anda buat dan kelola. Pada halaman **Pusat manajemen**, Anda dapat memilih **Sumber daya tersambung**, baik di bawah hub atau proyek Anda, dan mengamati bahwa koneksi ke Layanan Azure OpenAI dan AI telah dibuat.
1. Jika Anda berada di halaman Pusat manajemen, pilih **Buka proyek**.

## Menyebarkan dan menguji model

Anda dapat menggunakan proyek untuk membuat solusi AI yang kompleks berdasarkan model AI generatif. Eksplorasi penuh dari semua opsi pengembangan yang tersedia di Azure AI Foundry berada di luar cakupan latihan ini, tetapi kami akan menjelajahi beberapa cara dasar di mana Anda dapat bekerja dengan model dalam proyek.

1. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
1. Pada halaman **Model + titik akhir** , di tab **Penerapan model** pilih **+ Terapkan model**.
1. Cari model **gpt-35-turbo** dari daftar, pilih dan konfirmasi.
1. Terapkan model dengan pengaturan berikut dengan memilih **Sesuaikan** di detail penyeberan:
    - **Nama penyebaran**: *Nama unik untuk penyebaran model Anda*
    - **Tipe penyebaran**: Standar
    - **Versi model**: *Pilih versi default*
    - **Sumber daya AI**: *Pilih sumber daya yang dibuat sebelumnya*
    - **Batas Tarif Token Per Menit (ribuan)**: 5K
    - **Filter konten**: DefaultV2
    - **Aktifkan kuota dinamis**: Dinonaktifkan
      
    > **Catatan**: Mengurangi TPM membantu menghindari penggunaan berlebih kuota yang tersedia dalam langganan yang Anda gunakan. 5.000 TPM cukup untuk data yang digunakan dalam latihan ini.

1. Setelah model disebarkan, di halaman gambaran umum penyebaran, pilih **Buka di playground**.
1. Di halaman **obrolan playground** pastikan bahwa penyebaran model Anda dipilih di bagian **Penyebaran** .
1. Di jendela obrolan, masukkan kueri seperti *Apa itu AI?* dan lihat responsnya:

    ![Tangkapan layar playground di portal Azure AI Foundry.](./media/playground.png)

## Penghapusan

Setelah selesai menjelajahi Azure AI Foundry, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com?azure-portal=true) di tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
