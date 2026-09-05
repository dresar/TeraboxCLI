# TeraboxUploaderCLI
Alat CLI Python untuk melakukan pengunggahan (upload) ke cloud Terabox Anda dari lingkungan Linux atau Windows apa pun tanpa perlu menggunakan situs web.

## Konfigurasi

### Mendapatkan JS Token
Untuk menggunakan alat ini, Anda harus memiliki akun Terabox dan kunci JS Token. Anda bisa mendapatkan sesi JS Token dengan masuk (login) ke akun Terabox Anda dan mengikuti urutan langkah-langkah di bawah ini:

1. Buka cloud Terabox Anda.
2. Buka alat pengembang browser (Developer Tools) dengan menekan tombol **F12**.<br/>
![Developer tools F12](<images/devf12.png>)
3. Aktifkan "Device Toolbar" (ikon ponsel/tablet), lalu klik tombol panah kembali (back) untuk kembali ke tampilan Terabox.<br/> 
![Developer tools F12 "Device Toolbar"](<images/devf12devicetoolbar.png>) 
![Back Arrow](<images/backarrow.png>)
4. Pergi ke tab "Network".<br/>
![Developer tools F12 Network tab](<images/devf12network.png>)
5. Pilih filter "XHR".<br/>
![Developer tools F12 XHR filter](<images/devf12fetch.png>)
6. Klik direktori atau file apa saja di dalam cloud.
7. Cari permintaan (request) apa pun yang dibuat ke URL cloud Terabox dan klik permintaan tersebut.<br/>
![Developer tools F12 request item](<images/devf12list.png>)
8. Pilih tab "Payload".<br/>
![Developer tools F12 Payload tab](<images/devf12payload.png>)
9. Cari parameter `jsToken` dalam daftar tersebut dan salin nilainya.

Jika Anda tidak dapat menemukan parameter `jsToken`, coba pilih direktori atau file lain di cloud dan cari kembali parameter `jsToken` di payload permintaan. Pastikan Anda telah memilih filter "XHR" dan sedang melihat tab "Payload".


### Mendapatkan Nilai Cookies
Selain JS Token, Anda juga perlu mengambil nilai cookies. Anda dapat mendapatkannya dengan salah satu cara berikut:

#### Opsi 1: Menggunakan Ekstensi Cookie-Editor (Direkomendasikan)
Cara termudah untuk mendapatkan nilai cookies adalah menggunakan ekstensi browser.

1. Instal ekstensi **Cookie-Editor** dari Chrome Web Store: [Klik di sini untuk mengunduh](https://chromewebstore.google.com/detail/hlkenndednhfkekhgcdicdfddnkalmdm?utm_source=item-share-cb).
2. Buka tab Terabox Anda dan pastikan Anda sudah login.
3. Klik ikon ekstensi **Cookie-Editor** di toolbar browser Anda.
4. Anda akan melihat daftar cookies. Gunakan fitur pencarian (Search) di ekstensi untuk menemukan kunci berikut:
   - `csrfToken`
   - `browserid`
   - `ndus`
   - `ndut_fmt`
5. Klik pada setiap item tersebut, salin bagian **Value**, dan simpan untuk konfigurasi nanti.

#### Opsi 2: Cara Manual (Developer Tools)
Jika Anda tidak ingin menginstal ekstensi, ikuti langkah-langkah manual berikut:

1. Buka cloud Terabox Anda.
2. Buka alat pengembang browser (F12).<br/>
![Developer tools F12](<images/devf12.png>)
3. Pergi ke tab "Application".<br/>
![Developer tools F12 Application tab](<images/devf12apptab.png>)
4. Pilih item "Cookies" di panel kiri.<br/>
![Developer tools F12 Cookies tab](<images/devf12cookiestab.png>)
5. Cari nilai cookies yang diperlukan dan salin nilainya.<br/>
![Developer tools F12 Cookies values](<images/devf12cookieval.png>)

Anda perlu menyalin nilai `csrfToken`, `browserid`, `lang`, `ndus`, dan `ndut_fmt`. Langkah ini diperlukan agar alat ini kompatibel dengan API Terabox semaksimal mungkin. Meskipun ada beberapa parameter yang mungkin tidak digunakan secara langsung oleh API Terabox saat ini, ketiadaannya dapat menimbulkan masalah jika tidak disertakan dalam header permintaan.

## Membuat File JSON

### Pembuatan Template JSON Otomatis
Jika Anda tidak ingin membuat file JSON secara manual, Anda dapat menggunakan argumen `setup` untuk membuat file `secrets.json` dan `settings.json` secara otomatis. Jalankan perintah berikut di terminal:

```sh
python main.py setup
```

Setelah menjalankan perintah tersebut, alat ini akan meminta Anda untuk memasukkan kredensial Terabox dan nilai pengaturan program. Setelah diisi, alat akan membuat file `secrets.json` dan `settings.json` secara otomatis sesuai dengan nilai yang Anda berikan.

### Membuat file secrets.json
Buat file bernama `secrets.json` di direktori yang sama dengan file `main.py`. File ini menyimpan data otentikasi sensitif Anda. Berikut adalah struktur lengkapnya:

```json
{
  "jstoken": "masukkan_js_token_anda_di_sini",
  "cookies": {
    "csrfToken": "masukkan_csrf_token_anda_di_sini",
    "browserid": "masukkan_browser_id_anda_di_sini",
    "lang": "en",
    "ndus": "masukkan_ndus_token_anda_di_sini",
    "ndut_fmt": "masukkan_ndut_fmt_token_anda_di_sini"
  }
}
```

**Penjelasan Konfigurasi `secrets.json`:**

| Kunci (Key) | Deskripsi |
| :--- | :--- |
| `jstoken` | Token JavaScript yang didapatkan dari payload request (lihat panduan di atas). |
| `cookies.csrfToken` | Token CSRF untuk keamanan sesi. |
| `cookies.browserid` | ID unik browser Anda. |
| `cookies.lang` | Bahasa preferensi (biarkan `en` untuk bahasa Inggris). |
| `cookies.ndus` | Token otentikasi pengguna penting. |
| `cookies.ndut_fmt` | Token format data pengguna. |


### Membuat file settings.json
Buat file bernama `settings.json` di direktori yang sama dengan file `main.py`. File ini mengatur perilaku aplikasi. Berikut adalah struktur lengkap dengan semua opsi yang tersedia:

```json
{
  "directories": {
    "sourcedir": "C:/Path/Ke/Folder/Sumber/Anda",
    "remotedir": "/Folder/Tujuan/Di/Terabox",
    "uploadeddir": "C:/Path/Ke/Folder/Selesai/Upload"
  },
  "files": {
    "movefiles": "false",
    "deletesource": "false"
  },
  "encryption": {
    "enabled": "false",
    "encryptionkey": "C:/Path/Ke/File/Kunci/Enkripsi.key"
  },
  "ignoredfiles": [
    "file_rahasia.txt",
    "*.tmp",
    ".git"
  ],
  "appearance": {
    "showquota": "true"
  }
}
```

**Penjelasan Detail Konfigurasi `settings.json`:**

#### Bagian `directories` (Direktori)
*   `sourcedir`: Path absolut ke folder lokal yang berisi file-file yang ingin Anda upload.
*   `remotedir`: Path direktori di dalam cloud Terabox tempat file akan disimpan (contoh: `/Uploads`).
*   `uploadeddir`: Path absolut ke folder lokal tempat memindahkan file **setelah** sukses di-upload (hanya digunakan jika `movefiles` diset ke `true`).

#### Bagian `files` (Pengaturan File)
*   `movefiles`: (`"true"` atau `"false"`) Jika `true`, file akan dipindahkan ke `uploadeddir` setelah sukses di-upload.
*   `deletesource`: (`"true"` atau `"false"`) Jika `true`, file sumber akan **dihapus** setelah sukses di-upload.
    *   **PENTING:** Anda tidak boleh mengaktifkan `movefiles` dan `deletesource` secara bersamaan (salah satu harus `false`).

#### Bagian `encryption` (Enkripsi)
*   `enabled`: (`"true"` atau `"false"`) Jika `true`, file akan dienkripsi sebelum di-upload untuk keamanan tambahan.
*   `encryptionkey`: Path absolut ke file kunci enkripsi Anda. Jika file ini belum ada, program akan membuatnya secara otomatis saat pertama kali dijalankan dengan enkripsi aktif.

#### Bagian `ignoredfiles` (File yang Diabaikan)
*   Berisi daftar nama file atau pola (glob pattern) yang **tidak** akan di-upload.
*   Contoh: `["*.tmp", "desktop.ini"]` akan mengabaikan semua file `.tmp` dan file `desktop.ini`.

#### Bagian `appearance` (Tampilan)
*   `showquota`: (`"true"` atau `"false"`) Jika `true`, program akan menampilkan sisa kuota penyimpanan Terabox Anda saat berjalan.


## Dependensi
Alat ini menggunakan beberapa pustaka eksternal agar dapat bekerja dengan baik. Anda dapat menginstalnya dengan menjalankan perintah berikut di terminal:

```sh
pip install -r requirements.txt
```

### Instalasi Curl
#### Untuk Pengguna Linux dan macOS
Selain pustaka yang terdaftar di `requirements.txt`, Anda juga perlu menginstal `curl` di sistem Anda untuk melakukan proses upload ke Terabox. Alat ini akan mencoba menginstal curl secara otomatis jika belum ada (sesuai OS Anda). Namun, jika gagal, Anda perlu menginstalnya secara manual.

#### Untuk Pengguna Windows
Untuk Windows, alat ini akan mencoba menginstal curl menggunakan versi pre-built untuk Windows yang berlokasi [di sini](https://curl.se/windows/dl-8.5.0_5/curl-8.5.0_5-win64-mingw.zip). Anda juga dapat menginstal curl secara manual dengan mengikuti instruksi di [situs web curl](https://curl.se/windows/) untuk mengunduh binary dan menggunakan metode berikut:

1. Unduh file zip curl dari [situs web curl](https://curl.se/windows/).
2. Ekstrak file zip ke direktori pilihan Anda.
3. Tambahkan direktori tempat Anda mengekstrak file curl ke variabel lingkungan PATH sistem (Environment Variables).

Alternatifnya, Anda juga dapat menggunakan Windows Subsystem for Linux (WSL) untuk menjalankan alat ini di lingkungan Linux.


## Cara Penggunaan
Hal paling penting untuk menggunakan alat ini adalah memastikan **Python** sudah terinstal di sistem Anda. Jika belum, Anda dapat mengunduhnya dari [situs web Python](https://www.python.org/downloads/).

Untuk menjalankan alat ini:
1. Pastikan file `secrets.json` dan `settings.json` sudah dikonfigurasi dengan benar.
2. Pastikan dependensi di `requirements.txt` sudah terinstal.
3. Jalankan file `main.py` menggunakan perintah berikut di terminal:

```sh
python main.py
```

Alat ini akan memulai proses upload dan menampilkan kemajuan (progress) di konsol. Setiap kesalahan (error) yang terjadi selama proses upload akan ditampilkan di layar. Anda dapat memeriksa output terminal untuk melihat jika ada masalah.


## Pemecahan Masalah (Troubleshooting)
Jika Anda mengalami masalah saat menggunakan alat ini, silakan buka *issue* di bagian [Issues](https://github.com/dnigamer/TeraboxUploaderCLI/issues) repositori ini. Saya akan mencoba membantu Anda secepat mungkin.

Berikut adalah beberapa masalah umum yang mungkin Anda temui:

*   **Alat tidak dapat menginstal curl di sistem Anda.**
    *   Ini bisa terjadi jika Anda menggunakan OS yang tidak didukung atau karena alasan izin akses. Silakan instal curl secara manual.
*   **Alat tidak dapat menemukan file `secrets.json` atau `settings.json`.**
    *   Pastikan file-file tersebut berada di direktori yang sama persis dengan `main.py`.
    *   Periksa panduan di atas untuk memastikan Anda membuat file dengan nama dan format yang benar.
*   **Gagal mengunggah file ke Terabox.**
    *   Ini bisa terjadi jika `secrets.json` tidak dikonfigurasi dengan benar atau jika JS Token sudah kedaluwarsa (expired).
    *   Ulangi langkah pengambilan JS Token dan Cookies.
*   **Gagal memindahkan file ke direktori `uploadeddir`.**
    *   Pastikan path direktori di `settings.json` benar dan direktori tersebut sudah ada (dibuat) di komputer Anda.
*   **Gagal mengenkripsi file.**
    *   Pastikan konfigurasi enkripsi di `settings.json` benar.
    *   Pastikan program memiliki izin baca/tulis ke file kunci enkripsi.
*   **Gagal menghapus file sumber setelah upload.**
    *   Pastikan file sumber tidak sedang dibuka atau digunakan oleh program lain.
    *   Cek konfigurasi `deletesource` di `settings.json`.


