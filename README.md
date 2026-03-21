# 📖 Dokumentasi API

Dokumentasi ditujukan untuk developer untuk mengambil data melalui API.

---

## 📋 Daftar Isi

- [Akun](#-akun)
- [Cek Stok](#-cek-stok)
- [Beli Produk](#-beli-produk)
- [Transaksi](#-transaksi)

<a name="top"></a>

---

## � Akun

<details>
<summary><code>POST</code> <strong>/api/user/data</strong> - Cek Data Akun</summary>

Mengambil informasi akun user seperti username, saldo, email, dan tanggal terdaftar.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "telegram_id": 123456789,
  "action": "cek"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `telegram_id` | Telegram ID pemilik API key (wajib) |
| `action` | Harus diisi `"cek"` |

**Response Success (200)**
```json
{
  "username": "Mayugoro",
  "telegram_id": 123456789,
  "saldo": 50000,
  "email": "user@example.com",
  "terdaftar": "1 Juni 2020"
}
```

**Response Error (400) — telegram_id kosong**
```json
{
  "status": "error",
  "keterangan": "telegram_id wajib diisi"
}
```

**Response Error (401) — API key tidak disertakan**
```json
{
  "keterangan": "API key required"
}
```

**Response Error (403) — telegram_id tidak cocok dengan pemilik API key**
```json
{
  "status": "error",
  "keterangan": "telegram_id tidak cocok dengan pemilik API key"
}
```

**Response Error (404) — User tidak ditemukan**
```json
{
  "status": "error",
  "keterangan": "User tidak ditemukan"
}
```

**Catatan:**
- Field `saldo` berupa integer (satuan rupiah)
- Field `email` dan `username` menampilkan `"-"` jika belum diisi
- Field `terdaftar` berupa tanggal format Indonesia (contoh: `"1 Juni 2020"`)

</details>

---

## �📦 Cek Stok

<details>
<summary><code>GET</code> <strong>/api/cekstok/akrab</strong> - Cek Stok Produk Akrab</summary>

Mengambil daftar semua produk Akrab beserta stok dan harga dari database.

**Tidak memerlukan Authorization**

**Response Success (200)**
```json
{
  "status": "ok",
  "total_produk": "5",
  "response": [
    {
      "kode_produk": "AKRAB_1GB",
      "nama_produk": "Akrab 1GB",
      "kuota": "1",
      "harga": "5000",
      "stok": "100",
      "deskripsi": "Paket Akrab 1GB",
      "catatan": ""
    }
  ]
}
```

**Response Error (500)**
```json
{
  "status": "error",
  "message": "Failed to fetch akrab products"
}
```

</details>

<details>
<summary><code>GET</code> <strong>/api/cekstok/circle</strong> - Cek Stok Produk Circle</summary>

Mengambil daftar semua produk Circle beserta stok dan harga dari database.

**Tidak memerlukan Authorization**

**Response Success (200)**
```json
{
  "status": "ok",
  "total_produk": "5",
  "response": [
    {
      "kode_produk": "CIRCLE_1GB",
      "nama_produk": "Circle 1GB",
      "harga": "6000",
      "stok": "50",
      "deskripsi": "Paket Circle 1GB",
      "catatan": ""
    }
  ]
}
```

**Response Error (500)**
```json
{
  "status": "error",
  "message": "Failed to fetch circle products"
}
```

</details>

---

## 🛒 Beli Produk [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>POST</code> <strong>/api/stok/buy</strong> - Beli Produk Akrab</summary>

Membeli produk Akrab dari stok. Gunakan `kode_produk` dari hasil `/api/cekstok/akrab`. Saldo user akan dipotong sesuai harga produk.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "user_id": 123456789,
  "kode_produk": "AKRAB_1GB",
  "nomer_member": "08xxxxxxxxxx",
  "alias": "Nama Alias"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `user_id` | Telegram ID pemilik API key |
| `kode_produk` | Kode produk dari hasil `/api/cekstok/akrab` |
| `nomer_member` | Nomor tujuan |
| `alias` | Opsional (jika kosong, pakai nilai `nomer_member`) |

**Response Success (202 Accepted) — Transaksi sedang diproses**
```json
{
  "status": "pending",
  "keterangan": "Transaksi sedang diproses",
  "trx_id": "TRX-xxxx",
  "kode": "XL",
  "jenis": "Akrab 1GB",
  "saldo_awal": 50000,
  "saldo_terpotong": 5000,
  "saldo_akhir": 45000
}
```

**Response Error (400) — Format tidak valid**
```json
{
  "status": "error",
  "keterangan": "Format request tidak valid"
}
```

**Response Error (400) — Field wajib kosong**
```json
{
  "status": "error",
  "keterangan": "kode_produk dan nomer_member wajib diisi"
}
```

**Response Error (401) — API key tidak valid**
```json
{
  "status": "error",
  "keterangan": "API key tidak valid"
}
```

**Response Error (402) — Saldo tidak cukup**
```json
{
  "detail": "SALDO GAK CUKUP",
  "status": "error",
  "keterangan": "Saldo tidak mencukupi",
  "saldo_awal": 3000,
  "harga": 5000,
  "saldo_kurang": 2000
}
```

**Response Error (403) — user_id tidak cocok dengan pemilik API key**
```json
{
  "status": "error",
  "keterangan": "user_id tidak cocok dengan pemilik API key"
}
```

**Response Error (404) — Produk tidak ditemukan**
```json
{
  "status": "error",
  "keterangan": "Produk tidak ditemukan"
}
```

**Response Error (423) — Semua pengelola sedang sibuk**
```json
{
  "status": "error",
  "keterangan": "Semua nomor pengelola sedang dipakai, coba lagi sebentar"
}
```

**Catatan:**
- Response `202 pending` dikirim langsung, transaksi diproses di background
- Gunakan `trx_id` untuk mengecek status transaksi akhir (sukses/gagal)
- Jika transaksi gagal di background, saldo akan dikembalikan otomatis

</details>

<details>
<summary><code>POST</code> <strong>/api/stok/buy</strong> - Beli Produk Circle</summary>

Membeli produk Circle dari stok. Gunakan `kode_produk` dari hasil `/api/cekstok/circle`. Saldo user akan dipotong sesuai harga produk.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "user_id": 123456789,
  "kode_produk": "CIRCLE_1GB",
  "nomer_member": "08xxxxxxxxxx",
  "nama_member": "Nama Member"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `user_id` | Telegram ID pemilik API key |
| `kode_produk` | Kode produk dari hasil `/api/cekstok/circle` |
| `nomer_member` | Nomor tujuan |
| `nama_member` | Wajib diisi |

**Response Success (202 Accepted) — Transaksi sedang diproses**
```json
{
  "status": "pending",
  "keterangan": "Transaksi sedang diproses",
  "trx_id": "TRX-xxxx",
  "kode": "XL",
  "jenis": "Circle 1GB",
  "saldo_awal": 50000,
  "saldo_terpotong": 6000,
  "saldo_akhir": 44000
}
```

**Response Error (400) — Format tidak valid**
```json
{
  "status": "error",
  "keterangan": "Format request tidak valid"
}
```

**Response Error (400) — Field wajib kosong**
```json
{
  "status": "error",
  "keterangan": "kode_produk dan nomer_member wajib diisi"
}
```

**Response Error (400) — nama_member kosong**
```json
{
  "status": "error",
  "keterangan": "nama_member wajib diisi untuk produk circle"
}
```

**Response Error (401) — API key tidak valid**
```json
{
  "status": "error",
  "keterangan": "API key tidak valid"
}
```

**Response Error (402) — Saldo tidak cukup**
```json
{
  "detail": "SALDO GAK CUKUP",
  "status": "error",
  "keterangan": "Saldo tidak mencukupi",
  "saldo_awal": 3000,
  "harga": 6000,
  "saldo_kurang": 3000
}
```

**Response Error (403) — user_id tidak cocok dengan pemilik API key**
```json
{
  "status": "error",
  "keterangan": "user_id tidak cocok dengan pemilik API key"
}
```

**Response Error (404) — Produk tidak ditemukan**
```json
{
  "status": "error",
  "keterangan": "Produk tidak ditemukan"
}
```

**Response Error (423) — Semua pengelola sedang sibuk**
```json
{
  "status": "error",
  "keterangan": "Semua nomor pengelola sedang dipakai, coba lagi sebentar"
}
```

**Catatan:**
- Response `202 pending` dikirim langsung, transaksi diproses di background
- Gunakan `trx_id` untuk mengecek status transaksi akhir (sukses/gagal)
- Jika transaksi gagal di background, saldo akan dikembalikan otomatis

</details>

---

## 🧾 Transaksi [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>POST</code> <strong>/api/transaksi</strong> - Riwayat Transaksi User</summary>

Mendapatkan daftar riwayat transaksi milik user. Data mencakup transaksi pembelian dan riwayat deposit dari 35 hari terakhir.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "telegram_id": "123456789",
  "limit": "10"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `telegram_id` | Telegram ID pemilik API key (wajib) |
| `limit` | Jumlah maksimal data yang dikembalikan (opsional, default: 20, max: 1000) |

**Response Success (200)**
```json
{
  "status": "success",
  "transaksi": [
    {
      "id": "TRXU4UMMX2QJG",
      "jenis_trx": "akrab",
      "nomer_pembeli": "08xxxxxxxxxx",
      "tanggal_transaksi": "14 Januari 2026 10:48:04",
      "waktu_transaksi": "5 Jam yang lalu",
      "transaksi": "sukses",
      "telegram_id": "123456789",
      "saldo_awal": "1500000",
      "saldo_akhir": "1485000",
      "detail": "SUKSES BOS"
    }
  ],
  "deposit": [
    {
      "invoice_id": "INV20260114104804123",
      "telegram_id": "123456789",
      "username": "Mayugoro",
      "nominal_final": "50000",
      "saldo_awal": "1500000",
      "saldo_akhir": "1550000",
      "created_at": "14 Januari 2026 10:48:04",
      "tanggal_transaksi": "14 Januari 2026 10:48:04",
      "waktu_transaksi": "5 Jam yang lalu",
      "status": "paid"
    }
  ]
}
```

**Response Success (200) — Tidak ada transaksi**
```json
{
  "status": "success",
  "transaksi": [],
  "deposit": []
}
```

**Response Error (400) — telegram_id kosong**
```json
{
  "status": "error",
  "keterangan": "telegram_id atau trx_id wajib diisi"
}
```

**Response Error (401) — API key tidak disertakan**
```json
{
  "keterangan": "API key required"
}
```

**Response Error (403) — API key tidak valid**
```json
{
  "keterangan": "Invalid API key"
}
```

**Response Error (500) — Database error**
```json
{
  "status": "error",
  "keterangan": "Gagal mengambil data transaksi"
}
```

**Catatan:**
- Field `transaksi` berisi array transaksi pembelian (akrab/circle/dll)
- Field `deposit` berisi array riwayat deposit QRIS (status `paid` atau `expired`)
- Data diambil dari **35 hari terakhir**
- Field `waktu_transaksi` menampilkan waktu relatif dalam WIB, contoh: `"5 Jam yang lalu"`
- Field `saldo_awal`, `saldo_akhir`, `detail` menampilkan `"-"` jika data tidak tersedia

</details>

<details>
<summary><code>POST</code> <strong>/api/transaksi</strong> - Cek Detail Transaksi</summary>

Mengecek status dan detail **satu transaksi** berdasarkan `trx_id`. Berguna untuk memantau hasil transaksi yang sebelumnya dikembalikan sebagai `pending`.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "trx_id": "TRXU4UMMX2QJG"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `trx_id` | ID transaksi yang diperoleh dari response `/api/stok/buy` |

**Response Success (200)**
```json
{
  "status": "success",
  "transaksi": {
    "id": "TRXU4UMMX2QJG",
    "jenis_trx": "akrab",
    "nomer_pembeli": "08xxxxxxxxxx",
    "tanggal_transaksi": "14 Januari 2026 10:48:04",
    "waktu_transaksi": "5 Jam yang lalu",
    "transaksi": "sukses",
    "telegram_id": "123456789",
    "saldo_awal": "1500000",
    "saldo_akhir": "1485000",
    "detail": "SUKSES BOS"
  }
}
```

**Response Error (404) — trx_id tidak ditemukan**
```json
{
  "status": "error",
  "keterangan": "Transaksi tidak ditemukan"
}
```

**Response Error (400) — Body tidak valid**
```json
{
  "status": "error",
  "keterangan": "Format body tidak valid"
}
```

**Response Error (401) — API key tidak disertakan**
```json
{
  "keterangan": "API key required"
}
```

**Response Error (403) — API key tidak valid**
```json
{
  "keterangan": "Invalid API key"
}
```

**Catatan:**
- Jika `trx_id` diisi, mode ini aktif dan `telegram_id` diabaikan
- Field `transaksi` mengembalikan **object tunggal** (bukan array)
- Tidak ada field `deposit` pada mode ini
- Field `transaksi` pada object bisa bernilai `"sukses"`, `"gagal"`, atau `"pending"`
- Field `detail` berisi pesan hasil dari provider (contoh: `"SUKSES BOS"`, `"Saldo tidak mencukupi"`)

</details>
