# Update Filter Checklist: Belum Checklist / Done / Semua

Update ini dibuat dari file `Code.gs.txt` dan `index.html` yang diberikan. Source mempertahankan aplikasi checklist saat ini; **tidak menambahkan atribusi runner, email, nama, atau slot**.

## Perilaku

- Tab awal: **Belum Checklist**. Task yang belum dicentang ditampilkan; task yang berhasil dicentang keluar dari daftar ini setelah data dimuat ulang.
- Tab **Done** menampilkan task yang telah dicentang.
- Tab **Semua** menampilkan semua task dan menyediakan checkbox untuk audit serta membatalkan checklist.
- Checkbox tetap bisa diubah pada tab Done; jika centang dibatalkan, task akan kembali ke Belum Checklist setelah refresh data.
- Angka tab Belum Checklist / Done / Semua dihitung atas seluruh task aktif (CSV setelah aturan H-1, ditambah ManualOrders) dan tidak berubah karena pencarian, kategori, status, atau pagination. Daftar yang terlihat tetap mengikuti filter-filter tersebut.
- Checklist tetap tersimpan pada tab `Checklist` berdasarkan Cache Order Task. Tidak ada perubahan schema spreadsheet.
- Aturan Binded >5, H-1, ManualOrders, kategori, CSRF, dan impor CSV tidak diubah.

## File

- `Code.gs`: backend yang mengembalikan daftar sesuai filter checklist dan jumlah tab.
- `index.html`: front end dengan tab baru, default Belum Checklist, dan reload setelah checklist berhasil disimpan.
- `tests/test_checklist_done.js`: simulasi Google Sheets untuk tes filter, tab count, perpindahan setelah checklist, serta undo.

## Pemasangan

1. Salin `Code.gs` ke project Apps Script yang dipakai aplikasi; pertahankan pengaturan target spreadsheet dan `parentOrigin` yang sudah ada.
2. Simpan, lalu deploy sebagai **versi baru** Web App agar endpoint menerapkan parameter `checklistFilter`.
3. Ganti `index.html` di repo GitHub Pages aktif dengan file pada paket ini dan commit ke branch/folder yang digunakan Pages.
4. Buka ulang GitHub Pages dan lakukan hard refresh.
5. Uji: centang satu task di Belum Checklist (task harus hilang dari tab itu dan muncul di Done); buka Semua, lepaskan centangnya (task harus kembali ke Belum Checklist).

Jika hanya front end yang diperbarui, sementara backend masih lama, daftar filter disembunyikan agar data tidak ditampilkan di tab yang menyesatkan; halaman menampilkan pemberitahuan bahwa Code.gs perlu diperbarui.

## Validasi yang dijalankan

```sh
node --check < Code.gs
node tests/test_checklist_done.js
```

Front end juga diperiksa dengan `node --check` terhadap script inline, pemeriksaan target ID, dan parsing CSS. Tes berjalan memakai simulasi spreadsheet lokal; belum merupakan verifikasi deployment/live.
