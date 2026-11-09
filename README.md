# Update Filter Checklist dan Binded Qty

Perubahan ini dibuat di atas file `Code.gs.txt` dan `index.html` yang diberikan. Source mempertahankan konfigurasi backend yang sudah ada. Tidak menambahkan atribusi runner, email, nama, atau slot.

## Perilaku checklist

- Tab awal **Belum Checklist**: task yang belum dicentang. Setelah checklist berhasil disimpan, daftar dimuat ulang sehingga task berpindah ke **Done**.
- **Done** menampilkan task yang dicentang.
- **Semua** menampilkan semua task agar bisa diaudit atau dicentangnya dibatalkan.
- Checklist dapat dibatalkan dari Done atau Semua; task kembali ke Belum Checklist setelah refresh.
- Hitungan Belum Checklist / Done / Semua mencakup seluruh task aktif (CSV setelah aturan H-1 ditambah ManualOrders) dan tidak berubah karena pencarian, kategori, status, filter Binded Qty, atau paginasi.

## Perilaku filter Binded Qty

- **Semua nilai**: tidak membatasi Binded Qty.
- **Rentang manual**: Min dan Max merupakan batas inklusif, contoh 5–15, 15–20, dan 30–40. Karena kedua batas termasuk, nilai 15 dapat cocok dengan rentang 5–15 maupun 15–20 jika keduanya dipilih secara terpisah.
- **Kelompok per 10**: isi awal kelompok kelipatan 10; misalnya awal 20 menampilkan nilai 20–29. Kelompok tidak tumpang tindih.
- **Kelompok per 100**: isi awal kelompok kelipatan 100; misalnya awal 100 menampilkan nilai 100–199. Kelompok tidak tumpang tindih.
- Filter Binded Qty dapat dikombinasikan dengan tab checklist, kategori, status, pencarian, dan paginasi.
- Nilai Binded diproses di backend sebelum pagination. Counter tab checklist tetap mencakup semua task aktif, tidak hanya hasil filter Binded.

## Batas perubahan

- Checklist tetap disimpan pada tab `Checklist` berdasarkan Cache Order Task. Tidak ada perubahan schema spreadsheet.
- Aturan impor Binded >5, H-1, ManualOrders, kategori, CSRF, dan proses impor CSV tidak diubah.
- Jika hanya front end yang diperbarui dan backend masih versi lama, kontrol Done/Binded disembunyikan dan halaman memberi tahu bahwa backend perlu diperbarui. Ini mencegah filter yang tampak aktif tetapi hasilnya sebenarnya tidak difilter.

## File

- `Code.gs`: backend filter checklist dan Binded Qty.
- `index.html`: UI tab Belum Checklist/Done/Semua, rentang manual dan bucket per 10/100.
- `tests/test_checklist_done.js`: tes integrasi simulasi spreadsheet untuk filter, hitungan, kombinasi kategori, perpindahan checklist, dan undo.
- `tests/test_frontend_contract.js`: tes sintaks inline JS, kontrol, payload API, dan ID HTML.

## Pemasangan

1. Ganti kode Apps Script dengan `Code.gs`, tetapi pertahankan konfigurasi deployment serta target spreadsheet/origin yang sesuai dengan aplikasi Anda.
2. Simpan dan deploy sebagai **versi Web App baru**. Endpoint harus menggunakan Code.gs yang menerima `checklistFilter` dan `bindedFilter`.
3. Ganti file `index.html` di lokasi repo/branch GitHub Pages yang aktif dan commit.
4. Buka situs Pages dan lakukan hard refresh.
5. Uji checklist: centang task di Belum Checklist, pastikan berpindah ke Done; dari Semua, batalkan centang dan pastikan kembali ke Belum Checklist.
6. Uji Binded Qty dengan contoh rentang 5–15, 15–20, 30–40, lalu kelompok per 10 awal 10 dan per 100 awal 100. Pastikan filter kategori dan tab checklist tetap bekerja bersama.

## Validasi yang dijalankan

```sh
node --check < Code.gs
node tests/test_checklist_done.js
node tests/test_frontend_contract.js
```

Script inline pada `index.html`, struktur ID/selector HTML, dan CSS juga diperiksa. Tes backend memakai simulasi spreadsheet lokal; deployment/live belum diverifikasi.
