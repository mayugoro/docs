# 🚀 XL BOT API Documentation 

API XL (Go)

---

## 📋 Daftar Isi

- [OTP](#otp-authentication)
- [SMS](#sms-management)
- [PULSA](#balance--pulsa)
- [MSISDN](#msisdn-management)
- [AKRAB](#akrab-management)
- [CIRCLE](#circle-management)
- [MASSAL](#massal-operations)
- [BUY](#buy-pembelian-paket)
- [PROFILE](#profile-settings)
- [PRODUK](#products-admin)
- [PAYMENT](#payment-methods-admin)
- [APIKEY](#api-key-management)

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
  "message": "API key berhasil dibuat",
  "data": {
    "telegram_id": 123456789,
    "your_apikey": "generated-api-key-here"
  }
}
```

**Response Error (400)**
```json
{
  "detail": "telegram_id is required"
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
  "message": "API key ditemukan",
  "data": {
    "telegram_id": 123456789,
    "your_apikey": "user-api-key-here",
    "created_at": "2024-01-01 12:00:00"
  }
}
```

**Response Error (404)**
```json
{
  "detail": "API key tidak ditemukan untuk telegram_id ini"
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
  "data": {
    "msisdn": "6281234567890",
    "otp_id": "otp-id-string"
  }
}
```

**Response Error (400)**
```json
{
  "status": "error",
  "detail": "Format nomor tidak valid"
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
  "telegram_id": 123456789,
  "otp_code": "123456"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Login berhasil",
  "data": {
    "msisdn": "6281234567890",
    "telegram_id": 123456789,
    "subscriber_id": "encrypted-subscriber-id"
  }
}
```

**Response Error (400)**
```json
{
  "status": "error",
  "detail": "nomer, telegram_id, and otp_code are required"
}
```

</details>

---

## 💰 Balance & Pulsa

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
    "remaining": 95,
    "expired_at": 1767632399,
    "pulsa": "Rp. 95",
    "tenggang": "05 Januari 2026"
  }
}
```

**Response Error (400)**
```json
{
  "error": "nomer is required"
}
```

**Response Error (404)**
```json
{
  "error": "Credential not found"
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
    "transaction_id": "TRX-12345",
    "pengirim": "6281234567890",
    "penerima": "6281234567899",
    "jumlah": 10000,
    "biaya_pengirim": 100,
    "biaya_penerima": 50,
    "total_debet": 10100,
    "status": "SUCCESS"
  }
}
```

**Response Error (400)**
```json
{
  "error": "PIN harus 6 digit angka"
}
```

**Response Error (404)**
```json
{
  "error": "Nomor pengelola tidak ditemukan atau token expired"
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

## 🔄 Massal Operations

<details>
<summary><code>POST</code> <strong>/api/circle/massal/add</strong> - Create Circle with Multiple Members</summary>

Membuat Circle baru sekaligus menambahkan beberapa member (maks 3).

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
  "nama_pengelola": "Owner",
  "nama_grub": "Family",
  "nomer_member1": "0811111111",
  "nama_member1": "Member 1",
  "nomer_member2": "0822222222",
  "nama_member2": "Member 2",
  "nomer_member3": "0833333333",
  "nama_member3": "Member 3"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "validation": {
      "all_valid": true,
      "validated_count": 3
    },
    "circle_created": true,
    "circle_info": {
      "group_name": "Family",
      "group_id": "GRP123",
      "parent_name": "Owner",
      "parent_msisdn": "6281234567890"
    },
    "members": [
      {
        "member_number": 1,
        "msisdn": "628111111111",
        "nama": "Member 1",
        "nama_original": "Member 1",
        "invitation_sent": true,
        "auto_accepted": true
      },
      {
        "member_number": 2,
        "msisdn": "628222222222",
        "nama": "Member 2",
        "nama_original": "Member 2",
        "invitation_sent": true,
        "auto_accepted": true
      },
      {
        "member_number": 3,
        "msisdn": "628333333333",
        "nama": "Member 3",
        "nama_original": "Member 3",
        "invitation_sent": true,
        "auto_accepted": true
      }
    ],
    "summary": {
      "total_members": 3,
      "successfully_added": 3,
      "auto_accepted": 3,
      "failed": 0
    },
    "message": "Circle berhasil dibuat dengan semua 3 member diterima"
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/akrab/massal/add</strong> - Add Member with Quota</summary>

Menambahkan member ke Akrab sekaligus set kuota otomatis (2 step dalam 1 request).

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
  "nomer_member": "081234567891",
  "alias": "John",
  "posisi_slot": 1,
  "kuber": 10
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "step_1_add_member": {
      "member_added": true,
      "posisi_slot": 1,
      "msisdn": "6281234567891",
      "alias": "John"
    },
    "step_2_set_kuber": {
      "quota_updated": true,
      "msisdn_member": "6281234567891",
      "quota_gb": 10,
      "quota_bytes": 10737418240,
      "quota_formatted": "10.00 GB"
    },
    "message": "Member berhasil ditambahkan dan kuota berhasil diset"
  }
}
```

**Response Partial Success (200)** - Member added but quota failed
```json
{
  "status": "partial_success",
  "data": {
    "step_1_add_member": {
      "member_added": true,
      "posisi_slot": 1,
      "msisdn": "6281234567891",
      "alias": "John"
    },
    "step_2_set_kuber": {
      "quota_updated": false,
      "error": "Error message here"
    },
    "message": "Member berhasil ditambahkan tapi kuota gagal diset"
  }
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
  "total_products": 10,
  "products": [
    {
      "product_key": "xtra_combo_8gb",
      "display_name": "Xtra Combo 8GB",
      "emoji": "📱",
      "price": "Rp 50000",
      "markup": "Rp 1000",
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
  "message": "Transaksi berhasil diproses",
  "data": {
    "msisdn": "6281234567890",
    "product_key": "xtra_combo_8gb",
    "product_name": "Xtra Combo 8GB",
    "price": "Rp 50000",
    "markup": "Rp 1000",
    "transaction_time": "18 Desember 2025 15:30 WIB",
    "xl_response": {
      "transaction_code": "TRX123456",
      "deeplink": "https://payment-url.com/...",
      "string": "Payment link or confirmation"
    }
  }
}
```

**Response Error (404)**
```json
{
  "error": "Produk tidak ditemukan"
}
```

**Response Error (404)**
```json
{
  "error": "Nomor 6281234567890 tidak terdaftar atau token expired"
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
  "data": {
    "total_checked": 1,
    "summary": {
      "valid": 1,
      "invalid": 0,
      "error": 0
    },
    "results": [
      {
        "msisdn": "6281234567890",
        "valid": true,
        "status": "valid",
        "message": "Member dapat bergabung",
        "response_code": "200-2001",
        "pesan_tambahan": "BISA JOIN CIRCLE BOS"
      }
    ]
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/info</strong> - Get Circle Info</summary>

Mendapatkan informasi detail Circle dan anggota (dengan decrypt MSISDN dan detail kuota).

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
    "msisdn": "6281234567890",
    "circle_info": {
      "group_id": "GRP123",
      "group_name": "Family Circle",
      "group_status": "ACTIVE",
      "owner_name": "Owner Name",
      "owner_msisdn": "6281234567890",
      "total_slots": 4,
      "used_slots": 2,
      "empty_slots": 2
    },
    "package": {
      "consumption": 5368709120,
      "allocation": 10737418240,
      "consumption_formatted": "5.00 GB",
      "allocation_formatted": "10.00 GB"
    },
    "members": {
      "parent": [
        {
          "msisdn": "6281234567890",
          "name": "Owner Name",
          "role": "PARENT",
          "status": "ACTIVE",
          "kuota": "10.00 GB",
          "slot_type": "FREE",
          "member_id": "MEM000",
          "join_date": "1 January 2024"
        }
      ],
      "children": [
        {
          "msisdn": "6281234567891",
          "name": "Member 1",
          "role": "CHILD",
          "status": "ACTIVE",
          "kuota": "5.00 GB",
          "slot_type": "FREE",
          "member_id": "MEM001",
          "join_date": "15 January 2024"
        }
      ],
      "total": 2
    }
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/create</strong> - Create Circle</summary>

Membuat grup Circle baru dengan member pertama (otomatis accept).

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
  "nama_pengelola": "Owner Name",
  "nama_grub": "Family Circle",
  "nomer_member": "081234567899",
  "nama_member": "First Member"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "circle_created": true,
    "member_auto_accepted": true,
    "message": "Circle berhasil dibuat dan member pertama sudah diterima otomatis",
    "circle_info": {
      "group_id": "GRP123",
      "group_name": "Family Circle",
      "parent_name": "Owner Name",
      "parent_msisdn": "6281234567890"
    },
    "first_member": {
      "msisdn": "6281234567899",
      "name": "First Member",
      "name_original": "First Member",
      "member_id": "MEM123",
      "status": "ACTIVE"
    }
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/add</strong> - Add Member</summary>

Menambahkan anggota ke Circle (otomatis accept).

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
  "data": {
    "invitation_sent": true,
    "member_auto_accepted": true,
    "message": "Member berhasil ditambahkan dan sudah diterima otomatis",
    "member": {
      "msisdn": "6281234567899",
      "name": "John Doe",
      "name_original": "John Doe",
      "member_id": "MEM456",
      "status": "ACTIVE"
    },
    "circle": {
      "group_id": "GRP123",
      "group_name": "Family Circle"
    }
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/kick</strong> - Kick Member</summary>

Mengeluarkan anggota dari Circle berdasarkan nomor member.

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
  "nomer_member": "081234567899"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "member_removed": true,
    "message": "Member berhasil dikeluarkan dari circle",
    "msisdn": "6281234567899",
    "member_id": "MEM456",
    "circle_dissolved": false
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/acc</strong> - Accept Invitation</summary>

Menerima undangan Circle secara manual (otomatis accept biasanya sudah dilakukan).

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
    "invitation_accepted": true,
    "message": "Berhasil join circle",
    "member": {
      "msisdn": "6281234567890",
      "member_id": "MEM123",
      "status": "ACTIVE"
    },
    "circle": {
      "group_id": "GRP123",
      "group_name": "Family Circle"
    }
  }
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
    "msisdn": "6281234567890",
    "has_bonus": true,
    "bonus_list": [
      {
        "name": "Bonus Kuota 5GB",
        "action_param": "PKG-123",
        "bonus_type": "LOYALTY"
      }
    ],
    "total_bonus": 1
  }
}
```

</details>

<details>
<summary><code>POST</code> <strong>/api/circle/klaim-bonus</strong> - Claim Bonus</summary>

Mengklaim bonus Circle berdasarkan nama bonus.

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
  "bonus_name": "Bonus Kuota 5GB"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "claimed": true,
    "bonus_name": "Bonus Kuota 5GB",
    "bonus_type": "LOYALTY",
    "action_param": "PKG-123",
    "destination": "6281234567890",
    "message": "Bonus berhasil dikirim ke 6281234567890"
  }
}
```

</details>

---

## ⚡ Akrab Management

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
  "message": "Family Plan info retrieved",
  "data": {
    "data": {
      "member_info": {
        "plan_type": "FAMILY_PLAN",
        "members": [
          {
            "msisdn": "6281234567890",
            "alias": "Parent",
            "member_type": "PARENT",
            "family_member_id": "encrypted_id"
          }
        ],
        "additional_members": [
          {
            "msisdn": "6281234567891",
            "alias": "Member 1",
            "member_type": "ADDITIONAL",
            "family_member_id": "encrypted_id",
            "usage": {
              "quota_allocated": 10737418240
            }
          }
        ]
      }
    }
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
  "alias": "John Doe",
  "posisi_slot": 1
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
  "posisi_slot": 1
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "member_removed": true,
    "msisdn_member": "6281234567891",
    "posisi_slot": 1,
    "message": "Member berhasil dihapus"
  }
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
  "posisi_slot": 1,
  "kuber": 10
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Quota set successfully",
  "data": {
    "posisi_slot": 1,
    "msisdn": "6281234567891",
    "kuber_gb": 10,
    "kuber_bytes": 10737418240,
    "quota_formatted": "10.00 GB",
    "original_quota": 0,
    "original_formatted": "0.00 B"
  }
}

</details>

---

## 📨 SMS Management

<details>
<summary><code>POST</code> <strong>/api/sms/cek</strong> - Check SMS/Notifications</summary>

Mengecek notifikasi yang masuk (OTP, Akrab, Pulsa, dll).

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
  "limit": 10
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "msisdn": "6281234567890",
    "owner_telegram_id": 123456789,
    "notifications": {
      "otp": [],
      "akrab_pengelola": [],
      "pulsa": [],
      "other": [],
      "total": 0
    }
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
  "telegram_id": 123456789,
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "msisdn": "6281234567890",
    "exists": true,
    "owner": {
      "telegram_id": 123456789,
      "subscriber_id": "SUB123",
      "subscription_type": "PREPAID",
      "created_at": "1 Januari 2024 10:00 WIB",
      "expired_token": "31 Desember 2024",
      "berlaku_hingga": "365 hari",
      "refresh_token": "token-string"
    }
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
  "telegram_id": 123456789,
  "nomer": "081234567890",
  "subscriber_id": "encrypted_subscriber_id",
  "refresh_token": "refresh_token_here",
  "subscription_type": "PREPAID"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Kredensial berhasil ditambahkan",
  "data": {
    "msisdn": "6281234567890",
    "telegram_id": 123456789,
    "subscriber_id": "encrypted_subscriber_id",
    "subscription_type": "PREPAID"
  }
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
  "telegram_id": 123456789,
  "nomer": "081234567890"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Nomor berhasil dihapus",
  "data": {
    "msisdn": "6281234567890"
  }
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
  "telegram_id": 123456789,
  "nomer": "081234567890",
  "nik": "1234567890123456",
  "kk": "1234567890123456"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Registrasi berhasil",
  "data": {
    "nomer": "081234567890",
    "msisdn": "6281234567890",
    "response_code": "200-00",
    "response_message": "Success",
    "raw_response": {}
  }
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
  "telegram_id": 123456789,
  "nomer": "081234567890",
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
  "total_methods": 3,
  "methods": [
    {
      "id": 1,
      "method_key": "balance",
      "method_name": "Balance",
      "method_type": "SETTLEMENT",
      "config": {},
      "description": "Payment via balance",
      "is_active": 1,
      "created_at": "2024-01-01",
      "updated_at": "2024-01-01"
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
  "method_key": "new_method",
  "method_name": "New Method",
  "method_type": "SETTLEMENT",
  "config": {},
  "description": "New payment method"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Payment method berhasil ditambahkan",
  "method_key": "new_method"
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
  "message": "Payment method berhasil dihapus",
  "method_key": "method_key"
}
```

</details>

---

## 📦 Products (Admin)

> **⚠️ Master Key Required** - Semua endpoint di kategori ini hanya bisa diakses dengan Master Key.

<details>
<summary><code>GET</code> <strong>/api/produk/list</strong> - List Products (Public)</summary>

Mendapatkan daftar semua produk dengan detail lengkap. Endpoint ini **tidak memerlukan autentikasi**.

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
  "total_products": 5,
  "products": [
    {
      "id": 1,
      "product_key": "kuota_5gb",
      "name": "Kuota 5GB",
      "display_name": "Kuota 5GB",
      "emoji": "📱",
      "price": "Rp 15000",
      "markup": "Rp 1000",
      "description": "Paket data 5GB",
      "packages": [],
      "payment_methods": ["balance"],
      "payment_for": "BUY_PACKAGE",
      "ask_overwrite": 0,
      "overwrite_amount": -1,
      "token_confirmation_idx": 0,
      "amount_idx": -1,
      "is_active": 1,
      "created_at": "2024-01-01",
      "updated_at": "2024-01-01"
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
  "product_key": "new_product",
  "name": "New Product",
  "display_name": "New Product",
  "emoji": "📱",
  "price": "Rp 10000",
  "description": "Description",
  "packages": [
    {
      "family_code": "FAM001",
      "variant_code": "",
      "order": 0,
      "is_enterprise": false,
      "migration_type": "NONE"
    }
  ],
  "method_key": "balance"
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Produk berhasil ditambahkan",
  "product_key": "new_product"
}
```

</details>

<details>
<summary><code>PUT</code> <strong>/api/produk/update/:product_key</strong> - Update Product (Master Key)</summary>

Mengupdate produk yang sudah ada. **product_key** dikirim di URL path.

**URL Example**
```
PUT /api/produk/update/xtra_combo_10gb
```

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
  "name": "Updated Name",
  "price": "Rp 12000",
  "is_active": 1
}
```

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Produk berhasil diupdate",
  "product_key": "product_key"
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
  "product_key": "product_key"
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
  "message": "Markup berhasil diset",
  "product_key": "kuota_5gb",
  "markup": "Rp 2000"
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

Untuk pertanyaan atau dukungan, silakan hubungi [developer](https://t.me/Mayugoro)

---

**© 2025 XL BOT API**

