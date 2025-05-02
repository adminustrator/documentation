---
slug: implementasi-env
title: Implementasi .env Pada Bruno
authors: [reza]
tags: [announcement, important]
---

Variabel `token` adalah salah satu variabel yang sangat sering bergonta-ganti sesuai keperluan user atau developer. Oleh karenanya variabel `token` harus dikostumisasi secara lebih personal agar tidak terlalu sering berganti saat developer melakukan `push`.

Silahkan `pull latest` dari branch **main**, lalu buat file baru dengan nama `.env` pada root folder dan salin isinya dari file `.env.example`. Mudahnya bisa dilakukan via Terminal dengan perintah:

```bash
cp .env.example .env
```

lalu ubah _value_ dari _key_ **TOKEN** tersebut dengan nilai yang Anda, _**developer**_, butuhkan dalam proses _development_. Misal:

```bash
TOKEN=oyAGniWKsKW7ElijwINeK/l0bKXgS8097R
```