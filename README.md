# Cache Order Task — implementation package

Paket ini berisi front end statis untuk GitHub Pages dan backend Google Apps Script yang memakai spreadsheet **Monitoring Runner** yang Anda berikan. Kode tidak menyertakan CSV operasional, password, token, atau data task.

## Fitur yang diimplementasikan

- Import CSV melalui front end; backend memvalidasi header wajib, task ID duplikat, waktu selesai, dan kuantitas sebelum mengganti data aktif.
- Data task disimpan pada tab `CacheOrders`.
- Checklist Paket Tipis disimpan terpisah pada `Checklist`, memakai `Cache Order Task` sebagai kunci. Import CSV tidak mengosongkan atau mengatur ulang tab checklist; status task lama tetap ada jika ID task muncul kembali.
- Riwayat import dan aturan filter disimpan pada `ImportLog`.
- Jika **waktu upload CSV** berada pada **00:01–12:00 WIB** (inklusif), tampilan membatasi `Complete Time` ke tanggal H-1 dari tanggal upload. Upload pada **12:01–23:59 WIB**, atau tepat **00:00**, tidak memakai filter H-1. Mode filter mengikuti import terakhir, bukan waktu operator membuka aplikasi.
- Kategori `Reguler` diberikan ketika `Task Remark` kosong/berisi spasi saja atau berisi penanda literal `blank` (tanpa membedakan kapitalisasi). Filter kategori tersedia untuk Reguler, Paket Tipis, Bulky, dan Liquid.
- Kategori Bulky mengenali token `bul`, `bulk`, `bulky`, awalan `bulk`, dan ejaan token yang berjarak edit paling banyak satu karakter dari `bulk`/`bulky`; Liquid dan Paket Tipis juga toleran terhadap ejaan satu karakter.
- Urutan awal selalu `Binded Order Quantity` terbesar ke terkecil, termasuk saat kategori, status, atau pencarian dipakai.
- Layout desktop berupa tabel dan layout mobile berupa kartu. Deskripsi pemilahan otomatis serta kartu metrik jumlah baris CSV tidak ditampilkan di antarmuka; aturan H-1 backend tetap berjalan seperti sebelumnya.
- CSV tidak dipasang di GitHub Pages. Koneksi lintas-origin memakai form POST dan iframe dengan nonce sekali pakai; endpoint hanya mengirim respons ke origin GitHub Pages yang disetel di backend.

## Setup spreadsheet

Spreadsheet target: `Monitoring Runner`.

Tab dan header yang digunakan:

- `CacheOrders`: 10 kolom sesuai skema CSV.
- `Checklist`: `Cache Order Task`, `Paket Tipis Checked`, `Updated At (WIB)`.
- `ImportLog`: `Imported At (WIB)`, `Filename`, `Rows Imported`, `H-1 Filter Applied`, `H-1 Date`.

`setupBackend()` aman dijalankan ulang: membuat tab yang belum ada dan memverifikasi header, tanpa menghapus isi baris data atau checklist. Tab dan header pada spreadsheet tujuan sudah disiapkan; `CacheOrders` berisi header saja sampai CSV pertama diunggah dari aplikasi setelah deployment. CSV lampiran belum dimasukkan ke spreadsheet.

## Langkah pemasangan

1. Buka spreadsheet `Monitoring Runner` → **Extensions → Apps Script**.
2. Salin `apps-script/Code.gs` ke proyek tersebut. Tampilkan `appsscript.json` dari Project Settings dan salin isi manifest dari paket ini.
3. Pada `APP_CONFIG.parentOrigin` di `Code.gs`, ganti `https://YOUR_GITHUB_USERNAME.github.io` dengan **origin persis** situs GitHub Pages (contoh bentuk: `https://nama-akun.github.io`, tanpa path `/nama-repo` dan tanpa `/` terakhir). Untuk custom domain, pakai origin custom domain itu.
4. Simpan, pilih fungsi `setupBackend`, lalu jalankan satu kali dan berikan izin spreadsheet kepada akun pemilik/deployer.
5. Deploy sebagai **Web app**, pilih **Execute as: Me / User deploying** dan **Who has access: Anyone with Google account / Anyone** (label UI dapat berbeda). Salin URL deployment yang berakhir `/exec`.
6. Di `github-pages/index.html`, ganti `PASTE_APPS_SCRIPT_WEB_APP_EXEC_URL` pada `APP_CONFIG.backendUrl` dengan URL `/exec` itu.
7. Buat repo GitHub, masukkan `github-pages/index.html` sebagai `index.html` di root branch yang dipakai Pages, lalu aktifkan GitHub Pages dari **Settings → Pages**.
8. Buka URL Pages memakai akun Google. Jika backend di dalam iframe belum merespons karena sesi login browser, buka URL Apps Script `/exec` pada tab baru untuk sign-in, kembali ke Pages, lalu muat ulang.
9. Uji dengan CSV salinan terlebih dahulu. Pastikan jumlah baris, jam upload WIB, jumlah checklist, filter kategori, dan urutan Binded sebelum dipakai operasional.

## Akses dan keamanan

Manifest mengatur `access: ANYONE`, yang berarti akun Google yang login—bukan akses anonim—dapat membuka endpoint. Aplikasi berjalan sebagai pemilik/deployer sehingga pengguna tidak perlu diberi hak edit langsung ke spreadsheet.

**Implikasi penting:** sesuai permintaan “semua user Gmail”, setiap akun Google yang dapat mencapai aplikasi dapat membaca data, mengganti CSV aktif, dan mengubah checklist. Jangan memakai deployment tersebut jika akses eksternal tidak memang dikehendaki. Untuk penggunaan internal saja, ubah akses deployment menjadi `DOMAIN` dan pastikan akun/administrator Workspace mengizinkannya. GitHub Pages dapat membuat kode front end terlihat publik; tidak ada data task yang diletakkan di repositori.

Jangan menaruh URL sensitif atau rahasia di file GitHub. Spreadsheet ID di backend adalah identifier, bukan kredensial; perlindungan data tetap berasal dari pembatasan web-app dan izin Google.

## File

- `apps-script/Code.gs` — backend, import CSV, filter H-1, klasifikasi kategori, checklist persisten.
- `apps-script/appsscript.json` — runtime, zona waktu, scope dan pilihan akses web app.
- `github-pages/index.html` — UI responsif desktop/mobile, tabel/kartu, pencarian, filter, sorting, paginasi, upload, dan koneksi backend.
- `tests/test_rules.js` — smoke tests untuk batas jam H-1 dan pencocokan kategori.

## Batas implementasi dari sesi ini

Source code dan skema spreadsheet disiapkan, tetapi deployment GitHub Pages dan publikasi versi Apps Script memerlukan akses ke repo GitHub serta otorisasi/deployer dari pemilik aplikasi. URL `/exec` baru tersedia setelah deployment dari akun pemilik.
