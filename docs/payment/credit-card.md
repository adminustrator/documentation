---
sidebar_position: 2
description: Credit Cards
---

# Credit Cards

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Pendaftaran Kartu

Ada beberapa hal yang diisi oleh customer saat menginput data kartunya, antara lain:

| Objek             | Status | Contoh              |
|-------------------| -- |---------------------|
| Nomor kartu       | wajib | _13 s.d. 19 digit_  |
| Bulan kedaluwarsa | wajib | _01 s.d. 12_        |
| Tahun kedaluwarsa | wajib | _2026_              |
| Nama depan        | wajib | _John_              |
| Nama belakang     | wajib | _Doe_               |
| Email             | wajib | _johndoe@gmail.com_ |
| Nomor handphone   | wajib | _628xxxx_           |
| CVV               | wajib | _3 s.d. 4 digit_    |

## Struktur Data

### `member_cards`

Adapun data di atas akan disimpan pada tabel `member_cards` dengan detail sebagai berikut:

| Kolom                  | Tipe Data | Panjang | Key     | Contoh                                     |
|------------------------|-----------|---------|---------|--------------------------------------------|
| id                     | bigint    | -       | primary | _1, 2, ... n_                              |
| member_username *      | string    | 50      | index   | _628xxxx_                                  |
| member_id *            | bigint    | -       | -       | _1, 2, ... n_                              |
| provider *             | string    | 50      | - | _XENDIT_ \| _RINTIS_, etc                  |
| token_id               | text      | -       | -       | _(encrypted)_                              |
| authentication_id      | text      | -       | -       | _(encrypted)_                              |
| status                 | string    | 20      | index   | _IN_REVIEW_ \| _VERIFIED_ \| _FAILED_, etc |
| first_name *           | string    | 100     | -       | _John_                                     |
| last_name *            | string    | 100     | -       | _Doe_                                      |
| email *                | string    | 255     | -       | _johndoe@gmail.com_                        |
| phone_number *         | string    | 25      | -       | _628xxxx_                                  |
| account_number         | string    | 20      | -       | _445653XXXXXX1096_ ***(masked)***          |
| exp_month *            | tinyint   | 2       | -       | _01 s.d. 12_                               |
| exp_year *             | year      | -       | -       | _2029_                                     |
| currency *             | char      | 3       | -       | _IDR_                                      |
| payer_authentication_url | text      | -       | -       | _https://..._                              |
| created_at             | timestamp | -       | -       | _2025-02-17 16:06:07.000_                  |
| updated_at             | timestamp | -       | -       | _2025-02-17 16:06:07.000_                  |
| deleted_at             | timestamp | -       | -       | _2025-02-17 16:06:07.000_                  |

Untuk kolom yang ada tanda asterisk (*) artinya itu tidak nullable.

### `member_card_logs`

Ini tabel untuk mencatat setiap transaksi pada kartu kredit yang terdaftar.

| Kolom          | Tipe Data | Panjang | Key     |
|----------------|-----------| -- |---------|
| id             | bigint    | - | primary |
| member_card_id | bigint    | - | index   |
| member_id      | bigint    | - | -       |
| request        | jsonb     | - | -       |
| response       | jsonb     | - | -       |
| created_at     | timestamp | - | -       |
| updated_at     | timestamp | - | -       |

request terdiri dari url, headers, method, dan body.

response terdiri dari status code, headers, dan body.

## Alur Proses

### Pendaftaran

Member melakukan pendaftaran kartu dengan detail yang tertulis pada [Pendaftaran Kartu](#pendaftaran-kartu).

### Autentikasi

Member diarahkan ke halaman sesuai dengan response pada `payer_authentication_url`.

### Redirection

Member akan diarahkan ke halaman **sukses** atau **gagal** terhadap proses autentikasinya.

:::info

Dibutuhkan dua halaman baru untuk mengakomodir kondisi ini.

:::

### Charge

Member memilih kartu untuk melakukan transaksi. Adapun yang digunakan saat charging adalah object `token_id`. Object ini juga terenkripsi agar tidak bisa sembarang dibaca dan digunakan.

## Alur Data

### Pendaftaran

Saat pendaftaran, maka akan masuk ke tabel `member_cards` sekaligus `member_card_logs`.

Pada tabel `member_cards`:
- id
- member_username
- member_id
- provider
- first_name
- last_name
- email
- phone_number
- exp_month
- exp_year
- currency

Kemudian melakukan request kepada provider dan mendapatkan response untuk update data sebelumnya, yakni:
- token_id
- authentication_id
- status
- account_number
- payer_authentication_url

Juga menyimpan request dan response-nya pada tabel `member_card_logs`.

## Alur OneSmile

### IPL Swakelola

Pada flow pembayaran IPL Swakelola, prosesnya tidak ada yang berubah, kecuali saat checkout. 

Selain untuk pembayaran dengan Kartu Kredit, endpoint checkout adalah `[residence-service]/wallet-checkout`. 

```bash
# dev
https://az-api-dev.onesmile.digital/residence-service/api/v2/wallet-checkout

# prod
https://aca-prd.az-api.onesmile.digital/residence-service/api/v2/wallet-checkout
```

Sedangkan untuk pembayaran menggunakan Kartu Kredit adalah `/payment-checkout` sebagaimana yang dipakai oleh servis Aktivitas (_Facility Booking_).

```bash
# dev
https://az-api-dev.onesmile.digital/payment-xendit/api/v2/payment-checkout

# prod
https://aca-prd.az-api.onesmile.digital/payment-xendit/api/v2/payment-checkout
```

Mengapa demikian? Karena ini adalah salah satu upaya untuk penyeragaman servis pembayaran.

Adapun request body-nya seperti ini:

```json
{
  "token_id": "pt-xxx-xxx", // string
  "order_id": 123,          // integer
  "mp_id": 123              // integer
}
```

Untuk `token_id` didapatkan dari object Kartu Kreditnya yang dijelaskan di bawah.

Adapun cara mengatasinya seperti metode menggunakan Dana yang menggunakan url lagi untuk autentikasi transaksinya.

## Object Kartu Kredit

Seluruh endpoint di bawah sudah terdokumentasi di Bruno, pada folder `Payment`, lalu subfolder `Credit Card`.

### Base URL

```bash
# dev
https://az-api-dev.onesmile.digital/payment-xendit/api/v2

# prod
https://aca-prd.az-api.onesmile.digital/payment-xendit/api/v2
```

### Registrasi Kartu

Endpoint

```
POST /cards
```

Request Body

```json
{
  "name": "John Doe",
  "email": "johndoe@gmai.com",
  "phone_number": "6285694859455",
  "account_number": "4000000000001000",
  "expiry": "12/29", // atau "12/2029"
  "cvv": "123",
  "success_to": "deeplink",
  "failure_to": "deeplink"
}
```

Response

```json
{
  "success": true,
  "msg": "Success",
  "data": {
    "type": "REDIRECT_CUSTOMER",
    "descriptor": "WEB_URL",
    "value": "https://redirect.xendit.co/authentications/6a1e9970b9300363ff2a8d1a/render?api_key=xnd_public_development_y51jVUKfSm06FKTguf9MBdZF0Me0bf2cxu3eUrfV5bbl9dBv0Xt46qYYSLeSBbE"
  }
}
```

Alternatif response lainnya terdokumentasi di Bruno.

Setelah melakukan autentikasi terhadap link yang diberikan, selanjutnya akan dikembalikan ke object `_to` sesuai dengan hasilnya.

### Listing Kartu

Endpoint

```
GET /cards
```

Response

```json
{
  "success": true,
  "msg": "Success",
  "data": [
    {
      "id": "11",
      "account_number": "400000XXXXXX2503",
      "first_name": "John",
      "last_name": "Doe",
      "brand": "VISA",
      "exp_month": 12,
      "exp_year": 2029,
      "token_id": "pt-430257a7-ece8-856a-a7b5-f191af161e41"
    }
  ],
  "meta": {
    "page_title": "Kartu Kredit",
    "empty_state": null
  }
}
```

Empty State

```json
{
  "success": true,
  "msg": "Success",
  "data": [],
  "meta": {
    "page_title": "Kartu Kredit",
    "empty_state": {
      "title": {
        "text": "Belum ada Kartu",
        "color": "#000000"
      },
      "subtitle": {
        "text": "Ayo tambahkan kartu kredit Anda",
        "color": "#000000"
      },
      "note": {
        "text": null,
        "color": null
      },
      "button": {
        "background_color": null,
        "border_color": "#F94E39",
        "caption": {
          "text": "Tambah Kartu Baru",
          "color": "#F94E39"
        }
      }
    }
  }
}
```

### Hapus Kartu

Endpoint

```
DELETE /cards/:card_id
```

Response

```json
{
  "success": true,
  "msg": "Success",
  "data": [
    {
      "id": "11",
      "account_number": "400000XXXXXX2503",
      "first_name": "John",
      "last_name": "Doe",
      "brand": "VISA",
      "exp_month": 12,
      "exp_year": 2029
    }
  ]
}
```