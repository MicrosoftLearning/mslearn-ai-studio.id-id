---
lab:
  title: Menjelajahi katalog model di Azure AI Studio
---

# Menjelajahi katalog model di Azure AI Studio

Katalog model Azure AI Studio berfungsi sebagai repositori pusat tempat Anda dapat menjelajahi dan menggunakan berbagai model, memfasilitasi pembuatan skenario AI generatif Anda.

Dalam latihan ini, Anda akan menjelajahi katalog model di Azure AI Studio.

> **Catatan**: Azure AI Studio sedang dalam pratinjau pada saat penulisan, dan sedang dalam pengembangan aktif. Beberapa elemen layanan mungkin tidak persis seperti yang dijelaskan, dan beberapa fitur mungkin tidak berfungsi seperti yang diharapkan.

> Untuk menyelesaikan latihan ini, langganan Azure Anda harus disetujui untuk akses ke layanan Azure OpenAI.

Latihan ini akan memakan waktu sekitar **25** menit.

## Membuat Azure AI Hub

Anda memerlukan Azure AI Hub di langganan Azure Anda untuk menghosting proyek. Anda dapat membuat sumber daya ini saat membuat proyek, atau menyediakannya sebelumnya (yang akan kita lakukan dalam latihan ini).

1. Di browser web, buka [https://ai.azure.com](https://ai.azure.com) dan masuk menggunakan kredensial Azure Anda.

1. Pada bagian Manajemen, pilih Semua hub, lalu pilih **+ Hub baru**. Buat proyek baru dengan pengaturan berikut:
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
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: Pilih untuk membuat Layanan AI baru atau gunakan yang sudah ada
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Buat**. Proses pembuatan mungkin memerlukan waktu beberapa menit untuk diselesaikan. Selama pembuatan hub, sumber daya AI berikut juga akan dibuat untuk Anda: 
    - Layanan AI
    - Akun Penyimpanan
    - Brankas kunci

1. Setelah Azure AI Hub dibuat, akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar detail Azure AI Hub di Azure AI Studio.](./media/azure-ai-overview.png)

## Membuat proyek

Azure AI Hub menyediakan ruang kerja kolaboratif tempat Anda dapat menentukan satu atau beberapa *proyek*. Mari kita buat proyek di Azure AI Hub Anda.

1. Di Azure AI Studio, pada halaman **Build** , pilih **+ Proyek baru**. Kemudian, di wizard **Buat proyek baru** , buat proyek dengan pengaturan berikut:

    - **Nama proyek**: *Nama unik untuk proyek Anda*
    - **Hub**: *AI Hub Anda*

1. Tunggu proyek Anda dibuat. Halaman ini akan terlihat mirip dengan gambar berikut:

    ![Cuplikan layar halaman detail proyek di portal Azure AI Studio.](./media/azure-ai-project.png)

1. Tampilkan halaman di panel di sisi kiri, perluas setiap bagian, dan perhatikan tugas yang bisa Anda lakukan dan sumber daya yang bisa Anda kelola dalam proyek.

## Terapkan model

Sekarang Anda sudah siap untuk menyebarkan model untuk digunakan melalui **Azure AI Studio**. Setelah disebarkan, Anda akan menggunakan model tersebut untuk menghasilkan konten bahasa alami.

1. Di Azure AI Studio, buat penyebaran baru dengan pengaturan sebagai berikut:

    - **Model**: gpt-35-turbo
    - **Tipe penyebaran**: Standar
    - **Sumber daya Azure OpenAI yang terhubung**: *Koneksi Azure OpenAI Anda*
    - **Versi model**: Pembaruan otomatis ke default
    - **Nama penyebaran**: *Nama unik pilihan Anda*
    - **Opsi tingkat lanjut**
        - **Filter konten**: Default
        - **Batas tarif token per menit**: 5K

> **Catatan**: Setiap model Azure AI Studio dioptimalkan untuk keseimbangan kemampuan dan performa yang berbeda. Kita akan menggunakan **model GPT 3.5 Turbo** dalam latihan ini, yang sangat mampu menghasilkan bahasa alami dan skenario obrolan.

## Menguji model di taman bermain obrolan

Mari kita lihat, bagaimana perilaku model ini dalam interaksi percakapan.

1. Navigasikan ke **Playground** di panel kiri.

1. Pada mode **Obrolan** masukkan perintah berikut di bagian **Sesi obrolan** 

    ```
   <Placeholder>
    ```

1. Model kemungkinan akan merespons dengan beberapa teks ...

## Mengubah pesan permintaan sistem

Di taman bermain obrolan, Anda dapat memodifikasi permintaan sistem untuk mengubah perilaku model. Perintah sistem adalah pesan awal yang diterima model sebelum mulai menghasilkan respons.

1. Di bagian **Pesan sistem** ubah pesan sistem ke teks berikut:

    ```
    <Placeholder>
    ```

1. Simpan perubahan pada pesan sistem.

1. Di bagian **Sesi obrolan**, masukkan ulang perintah berikut.

    ```
   <Placeholder>
    ```

8. Amati output, yang mudah-mudahan menunjukkan bahwa ...


## Menambahkan data di taman bermain obrolan

<Placeholder>

## Penghapusan

Setelah selesai dengan sumber daya Azure OpenAI Anda, ingatlah untuk menghapus penyebaran atau seluruh sumber daya di [portal Azure ](https://portal.azure.com/?azure-portal=true).

