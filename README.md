# 🛒 Dokumentasi API — Buyer

Dokumentasi untuk buyer yang ingin membeli produk **Akrab** & **Circle** via API.

---

## 📋 Daftar Isi

- [Akun](#-akun--kembali-ke-atas)
- [Cek Stok](#-cek-stok--kembali-ke-atas)
- [Beli Produk](#-beli-produk--kembali-ke-atas)
- [Transaksi](#-transaksi--kembali-ke-atas)

<a name="top"></a>

---

## 👤 Akun [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>POST</code> <strong>/api/v1/user/profile</strong> - Cek Profil Anda</summary>

Melihat data diri, saldo, kredensial akses, dan status akun Anda sendiri secara mandiri.

**Headers**
```http
Authorization: Bearer <API_KEY>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "x-pak": "<PUBLIC_API_KEY_SERVER>"
}
```

**Response Sukses (200 OK)**
```json
{
  "status": "success",
  "message": "Profil berhasil dimuat",
  "data": {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "created_at": "11 Juni 2026 : 12:00:00",
    "updated_at": "11 Juni 2026 : 12:00:00",
    "email": "johndoe@gmail.com",
    "is_blocked": false,
    "saldo": 150000,
    "nomer_login": 2,
    "telegram_id": "628123456789",
    "username": "johndoe",
    "merchant_expired_at": "12 Oktober 2026 : 14:30:00",
    "merchant_expired_in": "3 bulan 2 minggu 1 hari 4 jam 10 menit 5 detik",
    "url_webhook_notifikasi": "https://api.domain.com/webhook",
    "url_webhook_transaksi": "https://api.domain.com/webhook/trx",
    "api_key": "mayu_1234567890abcdef...",
    "merchant_key": "merch_abcdef...",
    "x_client": "1a2b3c4d5e6f7g8h9i0j"
  }
}
```
</details>

---

## 📦 Cek Stok [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>POST</code> <strong>/api/v1/stock/available</strong> - Cek Stok Publik (User)</summary>

Menampilkan agregasi sisa stok fisik yang sedang menganggur untuk seluruh produk.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "x-pak": "<PUBLIC_API_KEY_ANDA>"
}
```

**Response Sukses (200 OK)**
```json
{
  "status": "success",
  "stock_data": [
    {
      "code": "SUPERMINI",
      "name": "Akrab Super Mini",
      "category": "AKRAB",
      "price": 15000,
      "quota_gb": 10,
      "description": "Paket hemat anak kos",
      "stock": 4
    },
    {
      "code": "AKRAB-VIP",
      "name": "Akrab VIP Sultan",
      "category": "AKRAB",
      "price": 50000,
      "quota_gb": 100,
      "description": "Paket VIP sultan mantap",
      "stock": 0
    }
  ]
}
```
</details>

## 🛒 Beli Produk [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>POST</code> <strong>/api/v1/order/akrab</strong> - Beli Slot Akrab</summary>

Mengeksekusi pembelian slot Akrab. Sistem otomatis mengunci (*Lock*) slot di gudang, memotong saldo pengguna, dan melakukan *Kick* jika ada riwayat transaksi 11-35 hari sebelumnya (*Kill Logic*).

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "product_code": "AKRAB-VIP",
  "target_number": "087777111222",
  "voucher_code": "DISC1000"
}
```

> **Catatan:** `voucher_code` bersifat opsional. Kosongkan jika tidak menggunakan promo.

**Response Sukses (200 OK)**
```json
{
  "message": "Pesanan diterima dan sedang diproses",
  "status": "PENDING",
  "trx_id": "AKRAB-1a2b3c4d5e"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/order/circle</strong> - Beli Slot Circle</summary>

Mengeksekusi pembelian slot Circle dengan *Row-Level Locking*.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "product_code": "CIRCLE-JUMBO",
  "target_number": "087777111222",
  "voucher_code": "DISC1000"
}
```

> **Catatan:** `voucher_code` bersifat opsional. Kosongkan jika tidak menggunakan promo.

**Response Sukses (200 OK)**
```json
{
  "message": "Pesanan Circle diterima dan sedang diproses",
  "status": "PENDING",
  "trx_id": "CIRCLE-f1e2d3c4b5"
}
```
</details>

---

## 🧾 Transaksi [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code>GET</code> <strong>/api/v1/order/status/:trx_id</strong> - Cek Status Pesanan</summary>

Mengecek status pemrosesan transaksi pemesanan Akrab/Circle berdasarkan ID Transaksi.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
```

**Response Sukses (200 OK)**
```json
{
  "status": "success",
  "message": "Status pesanan berhasil diambil",
  "data": {
    "trx_id": "AKRAB-1a2b3c4d5e",
    "status": "SUCCESS",
    "product_code": "AKRAB-VIP",
    "target_number": "087777111222",
    "updated_at": "2026-06-11T12:00:00+07:00"
  }
}
```
</details>
