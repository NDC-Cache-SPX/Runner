# Update kategori Task Remark: MESIN, KACA, AL QUR'AN

Paket ini melanjutkan versi aplikasi yang sudah memiliki tab checklist **Belum Checklist / Done / Semua** dan filter **Binded Qty**. Source baru menambah kategori `MESIN`, `KACA`, dan `AL QUR'AN` yang dideteksi dari `Task Remark`.

## Logika kategori

- Pencocokan tidak membedakan huruf besar/kecil. Untuk `MESIN` dan `KACA`, kata yang berjarak satu edit karakter dari target tetap cocok; edit meliputi satu huruf kurang/lebih, satu huruf terganti, atau dua huruf bersebelahan tertukar. Contoh `MESI`, `MEISN`, `MEZIN`, `KAC`, dan `KCAA` dikenali, lalu ditampilkan di label kategori baku.
- Pencocokan tetap per kata utuh—bukan substring. `MESINAN` tidak cocok dengan `MESIN`, dan `KACANG` tidak cocok dengan `KACA` karena selisihnya lebih dari satu karakter. Namun, toleransi satu karakter dapat menimbulkan kecocokan dekat yang tidak dimaksud, misalnya `KATA` hanya berbeda satu huruf dari `KACA`; periksa contoh Task Remark operasional sebelum deploy.
- Untuk `AL QUR'AN`, apostrof lurus/lengkung, tanpa apostrof, tanda hubung, dan bentuk gabung dikenali. Bagian `QURAN` menerima satu edit karakter; ejaan `AL` tetap dipertahankan untuk mengurangi false positive. Contoh `AL QURAN`, `AL-QURAN`, `ALQURAN`, dan `AL QURAAN` dikenali.
- Label baru bersifat tambahan. Jika Task Remark mengandung `BULKY` dan `MESIN`, task memperoleh kedua label; kategori lama `Reguler`, `Paket Tipis`, `Bulky`, dan `Liquid` tetap dipertahankan.
- Filter kategori baru, counter kategori, serta badge desktop dan kartu mobile mengikuti data kategori dari backend.
- Filter kategori dapat digabung dengan checklist Done, filter Binded Qty, status, dan pencarian.

## File

- `Code.gs`: konstanta kategori, klasifikasi Task Remark, counter, dan filter server.
- `index.html`: tombol filter kategori dan badge warna untuk desktop/mobile.
- `tests/test_task_remark_categories.js`: tes klasifikasi dan filter backend.
- `tests/test_frontend_categories.js`: tes sintaks serta kontrol dan badge kategori di front end.

## Instalasi

1. Ganti kode Apps Script dengan `Code.gs`, lalu simpan. Header dan schema spreadsheet tidak berubah.
2. Deploy Apps Script sebagai versi Web App baru.
3. Ganti `index.html` di branch/folder GitHub Pages yang aktif dan commit.
4. Buka ulang halaman dengan hard refresh.
5. Uji Task Remark `mEsIn`, `KACA`, `Al Qur'An`, dan satu remark gabungan seperti `BULKY + MESIN + KACA + AL QUR'AN`.

Versi live belum diubah atau diverifikasi oleh paket ini. Ambang fuzzy yang dipakai adalah satu edit karakter/transpose untuk mengakomodasi typo dekat; cek data Task Remark aktual karena fuzzy matching bisa menambah false positive.

## Tes

```sh
node --check < Code.gs
node tests/test_task_remark_categories.js
node tests/test_frontend_categories.js
```
