# 🚀 XL BOT API Documentation

REST API untuk manajemen nomor XL Axiata - pembelian paket, transfer pulsa, manajemen Circle & Akrab.

---

## 📋 Daftar Isi

- [Authentication](#-authentication)
- [API Key Management](#-api-key-management)
- [OTP Authentication](#-otp-authentication)
- [Pulsa](#-pulsa)
- [Profile Settings](#-profile-settings)
- [Buy (Pembelian Paket)](#-buy-pembelian-paket)
- [Circle Management](#-circle-management)
- [Akrab Management](#-akrab-management)
- [SMS Management](#-sms-management)
- [MSISDN Management](#-msisdn-management)
- [Payment Methods (Admin)](#-payment-methods-admin)
- [Products (Admin)](#-products-admin)

---

## 🔐 Authentication

API menggunakan **API Key** yang dikirim melalui header `Authorization`.

### User API Key (Standard)
Untuk endpoint yang memerlukan autentikasi user:
```json
{
  "Authorization": "user-api-key-here",
  "Content-Type": "application/json"
}
```

### Master Key (Admin Only)
Untuk endpoint administratif (product, payment, msisdn management):
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

---

## 🔑 API Key Management

<details>
<summary><code>POST</code> <strong>/api/create</strong> - Create API Key</summary>

Membuat API Key baru untuk user. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "telegram_id": 123456789
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "api_key": "generated-api-key-here",
  "telegram_id": 123456789
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/view</strong> - View API Key (Master Key Required)</summary>

Melihat API Key user berdasarkan telegram_id. **Hanya bisa diakses dengan Master Key**.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "telegram_id": 123456789
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "api_key": "user-api-key-here",
  "telegram_id": 123456789
}
```

</details>

---

## 🔐 OTP Authentication

<details>
<summary><code>POST</code> <strong>/api/otp/req</strong> - Request OTP</summary>

Meminta kode OTP untuk login nomor XL. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "OTP berhasil dikirim",
  "expires_in": 300
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/otp/verif</strong> - Verify OTP</summary>

Memverifikasi kode OTP dan menyimpan credential. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "otp": "123456"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Login berhasil",
  "data": {
    "msisdn": "6281234567890",
    "subscriber_id": "encrypted-subscriber-id"
  }
}
```

</details>

---

## 💰 Pulsa

<details>
<summary><code>POST</code> <strong>/api/pulsa/cek</strong> - Cek Saldo Pulsa</summary>

Mengecek saldo pulsa dan expired date dari nomor yang terdaftar.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "balance": 50000,
    "expired_at": "2025-12-31"
  }
}
```

**Response Error (400)**
```json
{
  "error": "nomer is required"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/pulsa/kirim</strong> - Transfer Pulsa</summary>

Mengirim pulsa ke nomor lain.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "nomer_target": "081234567899",
  "jumlah": 10000,
  "pin": "123456"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Transfer pulsa berhasil",
  "data": {
    "amount": 10000,
    "target": "6281234567899"
  }
}
```

</details>

---

## 👤 Profile Settings

<details>
<summary><code>POST</code> <strong>/api/profile/status</strong> - Get Lock Status</summary>

Mengecek status fitur perlindungan (jaga pulsa, SMS banking, dll).

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "is_locked": true,
    "protections": ["RBTSMS", "SMSBANK", "DATA"]
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/profile/lock</strong> - Lock Protection</summary>

Mengaktifkan fitur perlindungan (jaga pulsa, SMS banking, dll).

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Proteksi berhasil diaktifkan"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/profile/unlock</strong> - Unlock Protection</summary>

Menonaktifkan fitur perlindungan.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Proteksi berhasil dinonaktifkan"
}
```

</details>

---

## 🛒 Buy (Pembelian Paket)

<details>
<summary><code>GET</code> <strong>/api/buy/list</strong> - List Products (Public)</summary>

Mendapatkan daftar produk yang tersedia. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "total": 10,
  "products": [
    {
      "product_key": "xtra_combo_8gb",
      "product_name": "Xtra Combo 8GB",
      "price": 50000,
      "description": "Kuota 8GB + Bonus"
    }
  ]
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/buy/trx</strong> - Buy Transaction</summary>

Melakukan pembelian paket data.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "product_key": "xtra_combo_8gb"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Pembelian berhasil",
  "data": {
    "transaction_code": "TRX123456",
    "product_name": "Xtra Combo 8GB",
    "price": 50000,
    "deeplink": "https://payment-url.com/..."
  }
}
```

</details>

---

## 🔵 Circle Management

<details>
<summary><code>POST</code> <strong>/api/circle/validate</strong> - Validate Member (Public)</summary>

Memvalidasi nomor sebelum ditambahkan ke Circle. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Nomor valid",
  "eligible": true
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/info</strong> - Get Circle Info</summary>

Mendapatkan informasi detail Circle dan anggota.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "group_name": "Family Circle",
    "total_members": 3,
    "total_quota": "25GB",
    "members": [
      {
        "msisdn": "6281234567890",
        "name": "Admin",
        "role": "PARENT"
      }
    ]
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/create</strong> - Create Circle</summary>

Membuat grup Circle baru.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "group_name": "Family Circle"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Circle berhasil dibuat",
  "data": {
    "group_id": "SCX_encrypted_id",
    "group_name": "Family Circle"
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/add</strong> - Add Member</summary>

Menambahkan anggota ke Circle.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "nomer_member": "081234567899",
  "nama_member": "John Doe"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Anggota berhasil ditambahkan",
  "data": {
    "member_id": "encrypted_member_id",
    "msisdn": "6281234567899"
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/kick</strong> - Kick Member</summary>

Mengeluarkan anggota dari Circle.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "member_id": "encrypted_member_id"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Anggota berhasil dikeluarkan"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/acc</strong> - Accept Invitation</summary>

Menerima undangan Circle secara manual.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "invitation_id": "encrypted_invitation_id"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Undangan berhasil diterima"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/cek-bonus</strong> - Check Bonus</summary>

Mengecek bonus Circle yang tersedia.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "total_bonus": 2,
    "bonuses": [
      {
        "title": "Welcome Bonus 250MB",
        "description": "Bonus kuota"
      }
    ]
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/klaim-bonus</strong> - Claim Bonus</summary>

Mengklaim bonus Circle.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "bonus_id": "encrypted_bonus_id"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Bonus berhasil diklaim"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/massal/add</strong> - Add Multiple Members</summary>

Menambahkan beberapa anggota sekaligus ke Circle.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "members": [
    {
      "nomer_member": "081234567891",
      "nama_member": "John"
    },
    {
      "nomer_member": "081234567892",
      "nama_member": "Jane"
    }
  ]
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "2 anggota berhasil ditambahkan",
  "results": [
    {
      "msisdn": "6281234567891",
      "status": "success"
    }
  ]
}
```

</details>

---

## 👨‍👩‍👧 Akrab Management

<details>
<summary><code>POST</code> <strong>/api/akrab/info</strong> - Get Akrab Info</summary>

Mendapatkan informasi detail Akrab dan anggota.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "total_slots": 3,
    "members": [
      {
        "slot": 1,
        "msisdn": "6281234567891",
        "alias": "Member 1",
        "kuota_bersama": 10
      }
    ]
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/akrab/add</strong> - Add Member</summary>

Menambahkan anggota ke Akrab.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "nomer_member": "081234567899",
  "nama_member": "John Doe",
  "slot": 1
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Anggota berhasil ditambahkan"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/akrab/kick</strong> - Kick Member</summary>

Mengeluarkan anggota dari Akrab.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "slot": 1
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Anggota berhasil dikeluarkan"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/akrab/kuber</strong> - Set Kuota Bersama</summary>

Mengatur kuota bersama untuk anggota Akrab.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "slot": 1,
  "kuota_gb": 10
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Kuota bersama berhasil diatur"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/akrab/massal/add</strong> - Add Multiple Members</summary>

Menambahkan beberapa anggota sekaligus ke Akrab.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "members": [
    {
      "nomer_member": "081234567891",
      "nama_member": "John",
      "slot": 1,
      "kuota_gb": 10
    }
  ]
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Anggota berhasil ditambahkan"
}
```

</details>

---

## 📨 SMS Management

<details>
<summary><code>POST</code> <strong>/api/sms/cek</strong> - Check SMS</summary>

Mengecek SMS yang masuk (untuk validasi pembelian addon).

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "total_sms": 5,
    "messages": [
      {
        "from": "MyXL",
        "message": "Pembelian berhasil",
        "timestamp": "2025-12-18 10:00:00"
      }
    ]
  }
}
```

</details>

---

## 📱 MSISDN Management

> **⚠️ Master Key Required** - Semua endpoint di kategori ini hanya bisa diakses dengan Master Key.

<details>
<summary><code>POST</code> <strong>/api/msisdn/cek</strong> - Check MSISDN (Master Key)</summary>

Mengecek status nomor di database.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "telegram_id": 123456789
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "msisdn": "6281234567890",
    "telegram_id": 123456789,
    "is_active": true
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/msisdn/add</strong> - Add MSISDN (Master Key)</summary>

Menambahkan nomor baru ke database.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "telegram_id": 123456789,
  "subscriber_id": "encrypted_subscriber_id",
  "refresh_token": "refresh_token_here",
  "subscription_type": "PREPAID"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Nomor berhasil ditambahkan"
}
```

</details>

<details>
<summary><code>DELETE</code> <strong>/api/msisdn/revoke</strong> - Revoke MSISDN (Master Key)</summary>

Menghapus nomor dari database.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "telegram_id": 123456789
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Nomor berhasil dihapus"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/msisdn/regis</strong> - Register MSISDN</summary>

Melakukan registrasi nomor XL dengan NIK dan KK.

**Header**
```json
{
  "Authorization": "your-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "nik": "1234567890123456",
  "kk": "1234567890123456"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Registrasi berhasil"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/msisdn/pindah</strong> - Transfer MSISDN (Master Key)</summary>

Memindahkan kepemilikan nomor ke telegram_id lain.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "081234567890",
  "telegram_id": 123456789,
  "new_telegram_id": 987654321
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Nomor berhasil dipindahkan",
  "data": {
    "msisdn": "6281234567890",
    "old_telegram_id": 123456789,
    "new_telegram_id": 987654321
  }
}
```

</details>

---

## 💳 Payment Methods (Admin)

> **⚠️ Master Key Required** - Semua endpoint di kategori ini hanya bisa diakses dengan Master Key.

<details>
<summary><code>GET</code> <strong>/api/payment/list</strong> - List Payment Methods (Master Key)</summary>

Mendapatkan daftar semua metode pembayaran.

**Header**
```json
{
  "Authorization": "master-key-here"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "total_methods": 5,
  "methods": [
    {
      "id": 1,
      "method_key": "dana",
      "method_name": "DANA",
      "method_type": "ewallet",
      "is_active": 1
    }
  ]
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/payment/add</strong> - Add Payment Method (Master Key)</summary>

Menambahkan metode pembayaran baru.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "method_key": "ovo",
  "method_name": "OVO",
  "method_type": "ewallet",
  "config": {
    "fee": 0,
    "min_amount": 10000
  },
  "description": "OVO Payment"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Payment method berhasil ditambahkan",
  "method_key": "ovo"
}
```

</details>

<details>
<summary><code>DELETE</code> <strong>/api/payment/hapus/:method_key</strong> - Delete Payment Method (Master Key)</summary>

Menghapus (soft delete) metode pembayaran.

**Header**
```json
{
  "Authorization": "master-key-here"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Payment method berhasil dihapus"
}
```

</details>

---

## 📦 Products (Admin)

> **⚠️ Master Key Required** - Semua endpoint di kategori ini hanya bisa diakses dengan Master Key.

<details>
<summary><code>GET</code> <strong>/api/produk/list</strong> - List Products (Public)</summary>

Mendapatkan daftar semua produk. Endpoint ini **tidak memerlukan autentikasi**.

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "total": 10,
  "products": [
    {
      "product_key": "xtra_combo_8gb",
      "product_name": "Xtra Combo 8GB",
      "price": 50000,
      "category": "data",
      "is_active": 1
    }
  ]
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/produk/add</strong> - Add Product (Master Key)</summary>

Menambahkan produk baru.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "product_key": "xtra_combo_10gb",
  "product_name": "Xtra Combo 10GB",
  "price": 60000,
  "category": "data",
  "description": "Kuota 10GB",
  "payment_methods": ["dana", "ovo", "gopay"]
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Produk berhasil ditambahkan",
  "product_key": "xtra_combo_10gb"
}
```

</details>

<details>
<summary><code>PUT</code> <strong>/api/produk/update/:product_key</strong> - Update Product (Master Key)</summary>

Mengupdate produk yang sudah ada.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "product_name": "Xtra Combo 10GB Updated",
  "price": 65000
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Produk berhasil diupdate",
  "product_key": "xtra_combo_10gb"
}
```

</details>

<details>
<summary><code>DELETE</code> <strong>/api/produk/hapus/:product_key</strong> - Delete Product (Master Key)</summary>

Menghapus produk dari database.

**Header**
```json
{
  "Authorization": "master-key-here"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Produk berhasil dihapus",
  "product_key": "xtra_combo_10gb"
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/produk/markup</strong> - Set Product Markup (Master Key)</summary>

Mengatur markup harga produk.

**Header**
```json
{
  "Authorization": "master-key-here",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "product_key": "xtra_combo_10gb",
  "markup": 5000
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Markup berhasil diatur",
  "product_key": "xtra_combo_10gb",
  "markup": 5000
}
```

</details>

---

## 📝 Notes

- Semua response error akan mengembalikan format:
  ```json
  {
    "error": "Error message here"
  }
  ```
- Untuk endpoint dengan Master Key, gunakan environment variable `FASTAPI_API_KEY`
- Nomor telepon bisa dalam format `08xxx` atau `628xxx`, akan otomatis dinormalisasi
- Admin user dapat mengakses semua nomor tanpa validasi ownership

---

## 🚀 Base URL

```
Production: http://your-domain.com
Local: http://localhost:8001
```

---

## 📞 Support

Untuk pertanyaan atau dukungan, silakan hubungi admin.

---

**© 2025 XL BOT API**

