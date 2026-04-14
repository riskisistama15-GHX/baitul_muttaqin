# DKM Baitul Muttaqiin — Sistem Pendataan Remaja Masjid

## 📁 Struktur File

```
dkm-baitul-muttaqiin/
├── index.html                  ← Halaman Utama (Beranda)
├── pages/
│   └── daftar.html             ← Form Pendaftaran Anggota
├── admin/
│   ├── login.html              ← Login Admin
│   └── dashboard.html          ← Dashboard Admin (Data, Program, Pengaturan)
├── assets/
│   ├── css/
│   │   └── style.css           ← Stylesheet utama
│   └── js/
│       ├── main.js             ← JavaScript halaman utama
│       └── admin.js            ← JavaScript panel admin
└── README.md
```

## 🚀 Cara Menjalankan

### Versi Statis (Preview)
Cukup buka `index.html` di browser. Semua halaman sudah bisa dinavigasi.

- **Beranda**: `index.html`
- **Daftar**: `pages/daftar.html`
- **Admin Login**: `admin/login.html` (user: `admin`, pass: `admin123`)
- **Dashboard**: `admin/dashboard.html`

### Versi Produksi (PHP + MySQL)
1. Upload ke web hosting (cpanel, niagahoster, dll.)
2. Import database SQL di bawah
3. Konfigurasikan `config.php`
4. Ganti form action ke endpoint PHP

---

## 🗄️ Skema Database MySQL

```sql
-- Buat database
CREATE DATABASE IF NOT EXISTS dkm_baitul_muttaqiin CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE dkm_baitul_muttaqiin;

-- Tabel anggota remaja
CREATE TABLE remaja (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    nama        VARCHAR(150) NOT NULL,
    jenis_kelamin ENUM('Laki-laki','Perempuan') NOT NULL,
    tanggal_lahir DATE NOT NULL,
    umur        TINYINT UNSIGNED,
    alamat      TEXT NOT NULL,
    no_hp       VARCHAR(20) NOT NULL,
    pendidikan  VARCHAR(100),
    status      ENUM('Pelajar','Mahasiswa','Umum') NOT NULL,
    motivasi    TEXT,
    foto        VARCHAR(255),
    agree       TINYINT(1) DEFAULT 1,
    created_at  DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at  DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Tabel minat kegiatan (relasi many-to-many)
CREATE TABLE minat (
    id      INT AUTO_INCREMENT PRIMARY KEY,
    nama    VARCHAR(100) NOT NULL UNIQUE
);
INSERT INTO minat (nama) VALUES
    ('Pencak Silat'),('Tenis Meja'),('Futsal / Sepak Bola'),
    ('Badminton'),('Kajian Keagamaan'),('Kegiatan Ramadhan'),('Lainnya');

CREATE TABLE remaja_minat (
    remaja_id   INT NOT NULL,
    minat_id    INT NOT NULL,
    minat_lain  VARCHAR(100),
    PRIMARY KEY (remaja_id, minat_id),
    FOREIGN KEY (remaja_id) REFERENCES remaja(id) ON DELETE CASCADE,
    FOREIGN KEY (minat_id)  REFERENCES minat(id)  ON DELETE CASCADE
);

-- Tabel admin
CREATE TABLE admin_users (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    username    VARCHAR(50) NOT NULL UNIQUE,
    password    VARCHAR(255) NOT NULL,  -- bcrypt hash
    nama        VARCHAR(100),
    role        ENUM('super_admin','admin') DEFAULT 'admin',
    created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
);
-- Default: admin / admin123 (ganti password setelah deploy!)
INSERT INTO admin_users (username, password, nama, role)
VALUES ('admin', '$2y$12$HASH_BCRYPT_DI_SINI', 'Administrator', 'super_admin');

-- Tabel program kegiatan
CREATE TABLE program (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    nama        VARCHAR(100) NOT NULL,
    icon        VARCHAR(10),
    deskripsi   TEXT,
    jadwal      VARCHAR(100),
    status      ENUM('Aktif','Nonaktif') DEFAULT 'Aktif',
    created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## ⚙️ Konfigurasi PHP (config.php)

```php
<?php
define('DB_HOST', 'localhost');
define('DB_USER', 'root');         // ganti sesuai hosting
define('DB_PASS', 'password');     // ganti sesuai hosting
define('DB_NAME', 'dkm_baitul_muttaqiin');
define('UPLOAD_DIR', __DIR__ . '/assets/uploads/');
define('SITE_URL', 'https://yourdomain.com');

$conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);
$conn->set_charset('utf8mb4');
if ($conn->connect_error) die('Koneksi gagal: ' . $conn->connect_error);
```

---

## 📡 Endpoint PHP yang Dibutuhkan

| File | Fungsi |
|------|--------|
| `api/daftar.php` | POST — simpan data pendaftaran |
| `api/export-excel.php` | GET — export data ke .xlsx |
| `admin/proses-login.php` | POST — autentikasi admin |
| `admin/api/remaja.php` | GET/POST/PUT/DELETE — CRUD data |

---

## 🎨 Desain

- **Font**: Plus Jakarta Sans + Playfair Display (Google Fonts)
- **Warna Utama**: Hijau (#1D9E75, #0F6E56, #085041)
- **Aksen**: Gold (#c9a84c)
- **Responsif**: Mobile-first, breakpoint 768px & 1024px

## 📦 Library yang Dipakai (PHP Produksi)

```
composer require phpoffice/phpspreadsheet  ← Export Excel
composer require firebase/php-jwt          ← JWT Auth (opsional)
```

---

*Dibuat untuk DKM Baitul Muttaqiin — Sistem Pendataan Remaja Masjid*
