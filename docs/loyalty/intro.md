---
sidebar_position: 1
description: Selayang pandang voucher OneSmile — dua wujud voucher dan aturan tambahan yang bisa dipasang padanya.
---

# Pendahuluan

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

Voucher adalah hadiah atau potongan yang diterbitkan OneSmile untuk member: gratis
parkir, potongan harga di tenant, tiket masuk sebuah acara. Member melihatnya di
daftar voucher, **mengambilnya** (claim), lalu **menukarkannya** (redeem) saat
benar-benar dipakai.

Dokumen-dokumen di bagian ini menjelaskan logika di balik voucher dengan bahasa
sehari-hari: apa yang dilihat member, apa yang boleh dan tidak boleh terjadi, dan
kenapa aturannya dibuat seperti itu. Tidak ada nama berkas atau potongan kode di
sini — yang ada hanya perilakunya.

## Dua wujud voucher

Satu hal yang perlu dipahami lebih dulu, karena hampir semua aturan bergantung
padanya: **satu voucher hidup dalam dua wujud.**

| Wujud | Penjelasan |
| --- | --- |
| **Voucher induk** | Voucher yang diterbitkan tim OneSmile dan belum dimiliki siapa pun. Inilah yang tampil di katalog dan bisa diklaim. Satu voucher induk bisa diklaim banyak member. |
| **Voucher milik member** | Salinan yang dibuat khusus untuk seorang member pada saat dia menekan claim. Inilah yang tampil di "Voucher Saya" dan yang benar-benar ditukarkan. |

Saat member mengklaim, sistem tidak memindahkan voucher induk ke tangan member —
sistem **menyalinnya**. Voucher induk tetap di katalog untuk member lain, dan
member mendapat salinannya sendiri.

Yang penting: **salinan itu membawa semua aturan dari induknya.** Jadi aturan yang
dipasang di voucher induk tetap berlaku setelah voucher ada di tangan member, dan
aplikasi bisa membacanya langsung dari voucher milik member tanpa perlu menengok
kembali ke induknya.

## Aturan tambahan pada voucher

Setiap voucher punya kolom aturan tambahan — sebuah catatan kecil yang bisa diisi
tim OneSmile untuk mengubah perilaku voucher **tanpa perlu mengubah aplikasi**.
Voucher yang catatannya kosong berperilaku seperti voucher biasa; tidak ada satu
pun aturan di dokumen-dokumen berikut yang menyentuhnya.

Saat ini ada empat aturan yang dikenali:

| Aturan | Yang diubah | Dokumen |
| --- | --- | --- |
| Jadwal claim | Voucher sudah tampil, tapi baru boleh diklaim mulai tanggal tertentu | [Jadwal Claim](jadwal-claim.md) |
| Grup voucher | Beberapa voucher jadi satu paket pilihan — member ambil satu saja | [Grup Voucher](grup-voucher.md) |
| Redeem dengan kendaraan | Penukaran wajib mencatat kendaraan member yang dipakai | [Redeem dengan Kendaraan](redeem-dengan-kendaraan.md) |
| Masa tenggang setelah dipakai | Voucher yang sudah ditukar tetap di tab aktif sampai batas waktu tertentu | [Masa Tenggang](masa-tenggang-setelah-redeem.md) |

Keempatnya **berdiri sendiri** dan boleh dipasang bersamaan di satu voucher.
Kampanye parkir GIIAS 2026 adalah contoh nyata yang memakai empat-empatnya
sekaligus, dan dipakai sebagai contoh di sepanjang dokumen ini.

## Dari mana mulai membaca

Kalau baru pertama kali, baca [Siklus Hidup Voucher](siklus-hidup-voucher.md)
lebih dulu — di sana dijelaskan perjalanan voucher dari terbit sampai ditukar,
termasuk arti tab "Aktif" dan "Tidak Aktif" yang dilihat member. Keempat dokumen
aturan setelahnya bisa dibaca terpisah sesuai kebutuhan.

[Catatan Operasional](catatan-operasional.md) ditujukan untuk yang mengisi data
voucher: bagaimana menulis nilainya, apa yang terjadi kalau salah tulis, dan
jebakan yang sudah pernah ditemui.
