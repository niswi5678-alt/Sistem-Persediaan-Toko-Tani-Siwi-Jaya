# Sistem Persediaan Toko Tani Siwi Maju

Aplikasi persediaan berbasis HTML, CSS, dan JavaScript dengan database Supabase PostgreSQL. Frontend berkomunikasi langsung dengan Supabase menggunakan Supabase JavaScript CDN.

## Struktur Proyek

```text
back end/
  app.js
  schema.sql
front end/
  index.html
  css/style.css
  javascript/
    config.js
    supplier.js
    produk.js
    transaksiMasuk.js
    transaksiKeluar.js
.github/
  workflows/deploy-pages.yml
```

Database memiliki empat tabel inti: `supplier`, `produk`, `transaksi_masuk`, dan `transaksi_keluar`. Kategori disimpan sebagai kolom `produk.kategori`, bukan tabel tersendiri.

## 1. Siapkan Supabase

1. Buat atau pilih project di [Supabase Dashboard](https://supabase.com/dashboard).
2. Buka **SQL Editor** pada project tersebut.
3. Buka `back end/schema.sql`, salin SQL-nya ke SQL Editor, lalu jalankan.
4. Pastikan empat tabel terlihat di schema `public`: `supplier`, `produk`, `transaksi_masuk`, dan `transaksi_keluar`.

> **Peringatan:** `schema.sql` berisi `DROP TABLE ... CASCADE`. Menjalankannya akan menghapus tabel dan data sebelumnya sebelum membuat ulang skema. Jalankan hanya untuk database baru atau setelah memastikan data lama sudah dicadangkan.

## 2. Hubungkan Frontend ke Supabase

1. Di Supabase, buka **Project Settings > API** (atau bagian **API Keys** pada tampilan dashboard terbaru).
2. Salin **Project URL** dan **Publishable key**.
3. Buka `front end/javascript/config.js` dan isi nilainya:

```js
const SUPABASE_URL = 'https://YOUR_PROJECT_REF.supabase.co';
const SUPABASE_ANON_KEY = 'YOUR_PUBLISHABLE_KEY';
```

Nama konstanta `SUPABASE_ANON_KEY` dipertahankan demi kompatibilitas kode; nilainya dapat berupa Supabase publishable key yang berawalan `sb_publishable_`. Jangan gunakan `service_role` atau secret key di frontend.

## 3. Jalankan Lokal

Buka folder proyek di VS Code, lalu buka `front end/index.html` menggunakan ekstensi Live Server. Pastikan komputer terhubung ke internet karena Supabase JavaScript dimuat dari CDN dan data diakses melalui Supabase.

## 4. Unggah ke GitHub

Buat repository kosong di GitHub, lalu jalankan perintah ini dari folder utama proyek. Ganti URL remote dengan URL repository Anda:

```bash
git init
git add .
git commit -m "Initial sistem persediaan toko"
git branch -M main
git remote add origin https://github.com/USERNAME/NAMA-REPOSITORY.git
git push -u origin main
```

Jika repository sudah terhubung, perubahan berikutnya dapat dikirim dengan:

```bash
git add .
git commit -m "Perbarui sistem persediaan"
git push
```

File `config.js` berisi URL project dan publishable key yang digunakan browser. Publishable key memang dirancang untuk penggunaan frontend, tetapi siapa pun dapat melihatnya pada aplikasi/repository publik. Keamanan data harus diterapkan dengan **Row Level Security (RLS)** dan policies yang sesuai di Supabase. Aplikasi saat ini belum memiliki autentikasi pengguna dan SQL belum mengaktifkan RLS; jangan gunakan dengan data operasional atau membuka repository/aplikasi untuk umum sebelum akses database diamankan. Jangan pernah memasukkan `service_role` atau secret key ke GitHub.

## 5. Deploy Frontend dengan GitHub Pages

Workflow `.github/workflows/deploy-pages.yml` menerbitkan hanya folder `front end`, sehingga folder backend dan file lain tidak ikut disajikan sebagai website.

1. Push repository ke branch `main`.
2. Di GitHub, buka **Settings > Pages**.
3. Pada **Build and deployment > Source**, pilih **GitHub Actions**.
4. Buka tab **Actions** dan tunggu workflow **Deploy frontend to GitHub Pages** selesai.
5. Alamat website akan tampil di **Settings > Pages**. Push baru ke `main` akan memicu deployment berikutnya.

Perubahan pada `front end/javascript/config.js` harus ikut di-push agar website memakai konfigurasi Supabase yang benar.

## Catatan Arsitektur

Frontend melakukan query langsung ke Supabase untuk operasi supplier, produk, transaksi masuk, dan transaksi keluar. `back end/app.js` bukan server HTTP yang dijalankan oleh GitHub Pages; workflow Pages hanya menerbitkan frontend statis. File `back end/schema.sql` digunakan untuk menyiapkan database Supabase.

Skema SQL saat ini belum memiliki trigger untuk memperbarui `produk.stok` otomatis ketika transaksi masuk atau keluar dicatat. Nilai stok tidak berubah otomatis hanya dengan menyimpan transaksi.

## Troubleshooting

- **Tabel tidak ditemukan:** pastikan `schema.sql` berhasil dijalankan dan nama tabel/kolom sesuai.
- **Project URL/key belum berfungsi:** periksa nilai di `front end/javascript/config.js`, simpan, lalu push perubahan.
- **Aplikasi mendapat error permission/RLS:** periksa RLS dan policies Supabase. Jangan mengatasi masalah ini dengan memasukkan secret key ke frontend.
- **Website GitHub Pages belum muncul:** periksa log workflow di tab **Actions**, lalu pastikan Pages memakai source **GitHub Actions**.
