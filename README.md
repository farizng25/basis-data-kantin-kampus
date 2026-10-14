# 🍽️ Sistem Database Kantin

Project **Sistem Database Kantin Kampus** berbasis **SQL** dengan antarmuka **Gradio**. Sistem ini mengintegrasikan data mahasiswa (melalui identitas KTM/NPM), menu, kios, dan transaksi, sehingga pengelolaan kantin menjadi lebih terstruktur, akurat, dan mudah dianalisis.

---

## 📑 Daftar Isi

- [Latar Belakang](#-latar-belakang)
- [Tujuan](#-tujuan)
- [Fitur](#-fitur)
- [Teknologi](#-teknologi)
- [Rancangan Database](#-rancangan-database)
- [Implementasi SQL](#-implementasi-sql)
- [Struktur Fungsi Aplikasi](#-struktur-fungsi-aplikasi)
- [Cara Menjalankan](#-cara-menjalankan)
- [Hasil Pengujian](#-hasil-pengujian)
- [Kesimpulan](#-kesimpulan)

---

## 📖 Latar Belakang

Kantin kampus kerap menghadapi masalah operasional karena masih mengandalkan sistem manual, seperti pencatatan transaksi yang tidak sistematis, pelacakan stok yang tidak akurat, serta sulitnya menyusun dan mengaudit laporan keuangan. Proyek ini mengusulkan sistem basis data terintegrasi dengan identitas mahasiswa (KTM) agar setiap transaksi tercatat otomatis, data lebih aman dan terlacak, serta pengelola dapat mengambil keputusan berbasis data.

## 🎯 Tujuan

- Menyimpan dan mengelola data mahasiswa, produk (menu), dan transaksi secara terstruktur agar akurat dan konsisten.
- Menyediakan riwayat transaksi pembelian yang lengkap dan terorganisir untuk memudahkan pelacakan aktivitas pembelian, penyusunan laporan keuangan, dan analisis penjualan.

## ✨ Fitur

Sistem memiliki dua peran utama: **Pembeli** dan **Penjual**. Setelah login, tampilan dan fitur disesuaikan dengan peran pengguna.

### 🔐 Autentikasi
| Fitur | Deskripsi |
|-------|-----------|
| Login | Memeriksa email dan password, lalu mengenali peran pengguna (penjual/pembeli) |
| Register | Pendaftaran akun baru sebagai pembeli atau penjual; email yang sudah terdaftar akan ditolak |

### 🧑‍🍳 Fitur Penjual
- **Lihat Data**: melihat menu di kios sendiri, semua kios di kantin, dan transaksi yang melibatkan menu kiosnya
- **Tambah Menu**: menambah menu baru (ID menu dibuat otomatis berdasarkan ID kios)
- **Profil Kios**: melihat profil kios
- **Edit Menu**: mengubah nama, kategori, dan harga menu (hanya untuk kios miliknya)
- **Hapus Menu**: menghapus menu (hanya untuk kios miliknya)
- **Kelola Kios**: memperbarui nama kios, nama pedagang, email, dan password (email dan nama kios tidak boleh duplikat)

### 🛒 Fitur Pembeli
- **Lihat dan Update Profil**: melihat profil serta mengubah email, password, dan nomor telepon
- **Order**: memilih kios dan menu, menambah ke *cart*, mengosongkan *cart*, memilih metode pembayaran (Tunai / Non Tunai), dan mengonfirmasi order yang tercatat ke database

## 🛠️ Teknologi

| Teknologi | Kegunaan |
|-----------|----------|
| **Python** | Bahasa pemrograman utama |
| **SQLite** (`sqlite3`) | Basis data (`Basis Data Kantin.db`) |
| **Gradio** | Antarmuka pengguna berbasis web |
| **gdown** | Mengunduh file dari Google Drive (misalnya file database) |
| **datetime** | Pencatatan tanggal transaksi dan pembuatan ID transaksi |

## 🗄️ Rancangan Database

### Entitas
1. **Pembeli**: mahasiswa yang melakukan transaksi di kantin
2. **Transaksi**: data pembelian makanan/minuman
3. **Menu**: daftar menu yang tersedia
4. **Kios**: kios/pedagang yang berjualan di kantin
5. **Akun**: kredensial (email dan password) pembeli dan pedagang

### Relasi (ER Diagram)
<img width="578" height="380" alt="image" src="https://github.com/user-attachments/assets/72ba940c-b7b1-490a-a91a-13ab433f2275" />

Keterangan:
- `Pembeli` 1 — N `Transaksi` (melakukan)
- `Transaksi` N — M `Menu` (memuat)
- `Kios` 1 — N `Menu` (memiliki)
- `Pembeli` 1 — 1 `Akun`
- `Kios` 1 — 1 `Akun`

### Normalisasi
Data dinormalisasi bertahap dari **1NF → 2NF → 3NF**:
- **1NF**: tidak ada atribut multivalue dan baris duplikat
- **2NF**: memisahkan tabel Transaksi, Pembeli, dan Menu agar atribut non-key bergantung penuh pada primary key
- **3NF**: menghilangkan ketergantungan antar-atribut non-key dengan menambahkan tabel **Kios** dan **Akun**, serta foreign key `ID Kios` dan `ID Akun`

### Skema Tabel (3NF)

| Tabel | Kolom | Key |
|-------|-------|-----|
| **Akun** | `ID Akun`, `Email`, `Password` | PK: `ID Akun` |
| **Kios** | `ID Kios`, `Nama Kios`, `Nama Pedagang`, `ID Akun` | PK: `ID Kios` · FK: `ID Akun` → Akun |
| **Menu** | `ID Menu`, `Menu`, `Kategori`, `Harga`, `ID Kios` | PK: `ID Menu` · FK: `ID Kios` → Kios |
| **Pembeli** | `NPM`, `Nama`, `Jurusan`, `Nomor Telepon`, `ID Akun` | PK: `NPM` · FK: `ID Akun` → Akun |
| **Transaksi Penjualan** | `ID Transaksi_Menu`, `ID Transaksi`, `Tanggal`, `NPM`, `ID Menu`, `Jumlah`, `Metode Pembayaran` | PK: `ID Transaksi_Menu` · FK: `NPM` → Pembeli, `ID Menu` → Menu |

Kategori menu: **Makanan ringan**, **Makanan berat**, **Minuman**.

### Kios pada Data Contoh

| ID Kios | Nama Kios | Pedagang |
|---------|-----------|----------|
| 101 | ChickHub | Agus |
| 102 | Wok This Way | Shifu |
| 103 | Warkop Ndeso | Udin |
| 104 | Sapori D'Italia | Alfredo |

## 💾 Implementasi SQL

Contoh perintah SQL yang diimplementasikan:

```sql
-- CREATE TABLE (contoh: Akun)
CREATE TABLE "Akun" (
    "ID Akun"  INTEGER NOT NULL UNIQUE,
    "Email"    TEXT NOT NULL,
    "Password" TEXT NOT NULL,
    PRIMARY KEY("ID Akun" AUTOINCREMENT)
);

-- INSERT
INSERT INTO Pembeli ("NPM", "Nama", "Jurusan", "Nomor Telepon", "ID Akun")
VALUES ('2306164203', 'Budi', 'Statistika', '081234567800', '6');

-- UPDATE
UPDATE Pembeli SET Nama = 'Budi' WHERE NPM = '2306164405';

-- SELECT ... WHERE
SELECT * FROM Menu WHERE Kategori = 'Minuman';

-- SELECT ... ORDER BY
SELECT * FROM Menu ORDER BY Harga;

-- SELECT COUNT
SELECT COUNT(*) FROM "Transaksi Penjualan" WHERE "Metode Pembayaran" = 'Tunai';

-- JOIN ... ON
SELECT "Menu"."Menu", "Transaksi Penjualan"."Metode Pembayaran"
FROM "Transaksi Penjualan"
JOIN "Menu" ON "Transaksi Penjualan"."ID Menu" = "Menu"."ID Menu"
WHERE "Transaksi Penjualan"."NPM" = '2306164405';

-- DELETE
DELETE FROM "Transaksi Penjualan" WHERE "NPM" = '2306164405';
```

## 🧩 Struktur Fungsi Aplikasi

### Autentikasi
| Fungsi | Deskripsi |
|--------|-----------|
| `cek_login(email, password)` | Memeriksa kredensial dan menentukan peran (penjual/pembeli/tidak diketahui) |
| `proses_login(email, password)` | Mengatur tampilan halaman sesuai peran setelah login |
| `register_akun(...)` | Membuat akun baru, validasi field kosong, dan cek email duplikat |

### Penjual
| Fungsi | Deskripsi |
|--------|-----------|
| `lihat_profil_kios(id_akun)` | Menampilkan profil kios |
| `lihat_semua_menu_penjual(id_akun)` | Menampilkan menu di kios penjual |
| `lihat_semua_kios()` | Menampilkan seluruh kios di kantin |
| `lihat_trx_penjual(id_akun)` | Menampilkan transaksi pada kios penjual |
| `tambah_menu(nama, kategori, harga, id_akun)` | Menambah menu |
| `update_menu(id_menu, nama, kategori, harga, id_akun)` | Mengubah menu (dengan validasi kepemilikan kios) |
| `hapus_menu(id_menu, id_akun)` | Menghapus menu (dengan validasi kepemilikan kios) |
| `update_kios_dan_akun(...)` | Memperbarui info kios dan akun (validasi email dan nama kios unik) |

### Pembeli
| Fungsi | Deskripsi |
|--------|-----------|
| `ambil_profil_pembeli(id_akun)` | Menampilkan profil pembeli |
| `ubah_profil_pembeli(...)` | Mengubah email, password, dan nomor telepon |
| `tambah_ke_cart(nama_menu)` | Menambah menu ke cart |
| `lihat_cart()` | Melihat isi cart |
| `kosongkan_cart()` | Mengosongkan cart |
| `simpan_metode(metode)` | Memilih metode pembayaran (Tunai / Non Tunai) |
| `konfirmasi_order(hidden_id)` | Mencatat order ke database |

> **Catatan:** ID transaksi dibentuk dari kode tanggal (bulan + hari) diikuti nomor urut transaksi pada hari tersebut (3 digit). ID menu baru dibentuk dari ID kios sebagai prefiks, misalnya kios 101 menghasilkan menu 1001, 1002, dan seterusnya.

## 🚀 Cara Menjalankan

> Sesuaikan nama file dan path dengan struktur repositori Anda.

1. **Clone repositori**
   ```bash
   git clone https://github.com/<username>/<nama-repo>.git
   cd <nama-repo>
   ```

2. **Instal dependensi**
   ```bash
   pip install gradio gdown
   ```
   (`sqlite3` dan `datetime` sudah tersedia di pustaka standar Python.)

3. **Siapkan database**
   Pastikan file `Basis Data Kantin.db` tersedia dan variabel `DB_PATH` pada kode mengarah ke file tersebut. Jika database disimpan di Google Drive, gunakan `gdown` untuk mengunduhnya.

4. **Jalankan aplikasi**
   ```bash
   python app.py
   ```
   Lalu buka alamat lokal yang ditampilkan Gradio di browser.

## 🧪 Hasil Pengujian

Pengujian dilakukan melalui GUI dan mencakup skenario berikut:

| Modul | Skenario | Hasil |
|-------|----------|-------|
| Login | Login pembeli / penjual berhasil | Diarahkan ke halaman sesuai peran |
| Login | Email atau password salah | Muncul pesan "Email atau password salah." |
| Register | Pendaftaran berhasil | "Registrasi berhasil. Silakan login." |
| Register | Email sudah terdaftar | Ditolak dengan pesan peringatan |
| Register | Ada field kosong | Ditolak dengan pesan peringatan |
| Penjual | Tambah, edit, dan hapus menu di kios sendiri | Berhasil |
| Penjual | Edit/hapus menu kios lain | Ditolak ("Anda tidak memiliki akses...") |
| Penjual | Kelola kios dengan field kosong, email duplikat, atau nama kios duplikat | Ditolak dengan pesan yang sesuai |
| Pembeli | Lihat dan update profil (contoh: ganti nomor telepon) | Data berubah dan tampil di profil |
| Pembeli | Tambah ke cart, kosongkan cart, pilih metode pembayaran, confirm order | Order tercatat dan ID transaksi ditampilkan |

## ✅ Kesimpulan

Sistem berhasil menyimpan dan mengelola data mahasiswa, menu, kios, dan transaksi secara terstruktur dalam **5 tabel relasional** (Transaksi Penjualan, Pembeli, Menu, Kios, Akun). Riwayat transaksi yang lengkap melalui fitur **"Lihat Transaksi Saya"** membantu pedagang menganalisis menu paling favorit maupun yang jarang dibeli sebagai bahan evaluasi kios.

---

## 📚 Referensi

- Mandel, T. (2002). *The Elements of User Interface Design*.
- Muhtadi, M. M., Friyadi, M. D., & Rahmani, A. (2019).
