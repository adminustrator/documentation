---
sidebar_position: 1
description: Gambaran umum servis pembayaran IPL & Air (one-smile-payment-svc).
---

# Pendahuluan

:::tip[Penyusun]

- Bazrira Noerfirdiansyah :: Fullstack Developer Senior Associate - Operational Technology

:::

## Repositori

Dokumentasi ini membahas servis **`one-smile-payment-svc`** — sebuah backend berbahasa **Go**
yang menangani tagihan **IPL** (Iuran Pemeliharaan Lingkungan) dan **Air** untuk pelanggan
residensial/komersial di ekosistem Sinarmas Land / BSD.

Servis ini berbeda dari kategori [Xendit Payment Gateway](../payment/intro.md): repositori ini
memiliki basis kode, deployment, dan basis data tersendiri, serta menggunakan **RealBit** sebagai
sumber data tagihan.

## Kapabilitas utama

Yang dibahas pada rilis dokumentasi ini (**API-first**):

- Menampilkan daftar IPL milik member (`GET /ipl`).
- Statistik pembayaran per bulan (`GET /ipl/statistic`).
- Ringkasan tagihan (`GET /ipl/bill/summary`) dan riwayat tagihan (`GET /ipl/bill/history`)
  dari RealBit.
- Periode/tenor pembayaran (`GET /ipl/payment/period`).
- Banner _special offer_ (`GET /ipl/banner/special-offer`).
- Unduhan kwitansi & surat tagihan dalam bentuk **PDF** (`/receipt-ipl/...`).
- Endpoint **internal** untuk webhook billing bulanan RealBit dan unduhan laporan mingguan CSV.

Detail lengkap ada di halaman [Referensi API](./api-reference.md).

## Base path & host

Seluruh endpoint berada di bawah base path **`/api/v2`**. Host per environment mengikuti tabel
pada halaman [Base URLs](../base-url.md). Contoh path absolut:
`https://kube-prd.az-api.onesmile.digital/api/v2/ipl`.

## Konvensi bersama

- **Autentikasi** — bergantung pada grup endpoint:
  - Endpoint IPL memakai header **`X-Auth-Token`** (format `Bearer <token>`).
  - Endpoint internal memakai header **`api-key`** (JWT).
  - Unduhan PDF riwayat tagihan memakai **signed URL** (parameter `token` berbatas waktu).
- **Response envelope** — mayoritas endpoint JSON mengembalikan bentuk berikut:

  ```json
  {
    "status": "OK",
    "code": 200,
    "data": {},
    "message": null,
    "error": null
  }
  ```

  `data`, `message`, dan `error` bersifat `omitempty` (tidak muncul bila kosong). Endpoint yang
  berpaginasi menambahkan objek `pagination`. Endpoint PDF/CSV mengembalikan _file stream_, bukan
  envelope ini.

## Cakupan & rencana lanjutan

Rilis ini fokus pada **referensi API** dan panduan operasional
[Menambah Kota Baru (Multicity)](./multicity.md). Halaman arsitektur, model data, dan detail
integrasi eksternal akan menyusul pada revisi berikutnya.
