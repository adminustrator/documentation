---
sidebar_position: 1
description: Gambaran umum tentang servis pembayaran menggunakan Xendit.
---

# Pendahuluan

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Repositori

Belum ada repositori khusus yang menangani pembayaran, karena proses pembayaran dilakukan tersendiri untuk setiap servisnya. Misalnya, untuk repositori `Onesmile-Residence-Service` menggunakan file engine `xendit.js`, lalu repositori `Onesmile-Facility-Booking` menggunakan repositori terpisah yaitu `Onesmile-Payment-Xendit`. Juga ada beberapa servis yang menggunakan repositori `one-smile-api-v2`. Belum ada penyeragaman atau sentralisasi fitur payment atau pembayaran. Jadi, dokumentasi ini hanya membahas segi teknis umumnya saja, bukan berdasarkan repositori tertentu.

## Kanal Pembayaran

Kanal pembayaran atau _payment channel_ yang dipakai oleh OneSmile, antara lain:

- Virtual Accounts
- QR Codes
- eWallets, dan
- Credit Cards

> Saat dokumentasi ini ditulis, semua kanal sudah terimplementasi, kecuali kanal Credit Cards. Maka penulis merancang tentang kanal Credit Cards terlebih dahulu yang baru ingin diimplementasi.

