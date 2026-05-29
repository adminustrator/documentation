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
| currency *             | string    | 5       | -       | _IDR_                                      |
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