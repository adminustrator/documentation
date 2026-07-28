---
sidebar_position: 3
description: Voucher yang sudah tampil di aplikasi tapi baru boleh diklaim mulai tanggal tertentu.
---

# Jadwal Claim

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Masalah yang diselesaikan

Kampanye kadang perlu **diumumkan lebih dulu, dibuka kemudian**. Voucher parkir
sebuah pameran misalnya: tim ingin member sudah melihatnya sejak awal pekan supaya
bisa merencanakan kunjungan, tapi kuotanya baru boleh diperebutkan mulai hari
Senin pukul 00.00.

Tanpa aturan ini pilihannya hanya dua-duanya buruk: menerbitkan voucher lebih awal
dan membiarkan kuotanya habis sebelum waktunya, atau menerbitkan tepat pada
harinya dan kehilangan kesempatan mengumumkan.

## Yang dilakukan aturan ini

Voucher **tetap tampil** di katalog dan tetap bisa dibuka detailnya. Yang berubah
hanya satu: tombol claim mati sampai tanggal yang ditentukan tiba.

Ini beda dengan [grup voucher](grup-voucher.md) yang menyembunyikan voucher dari
daftar. Di sini voucher justru sengaja dipamerkan — memang itu tujuannya.

## Perilaku

| Keadaan | Yang dilihat member | Kalau tetap mencoba claim |
| --- | --- | --- |
| Sebelum tanggal yang ditentukan | Voucher tampil, tombol claim mati | Ditolak: *Voucher ini belum bisa diklaim* |
| Tepat pada atau setelah tanggal itu | Tombol claim hidup | Berjalan normal |
| Voucher tanpa aturan ini | Tidak terpengaruh | Tidak terpengaruh |
| Nilainya salah tulis | Tidak terpengaruh — aturan diabaikan | Berjalan normal |

Begitu tanggalnya lewat, aturan ini tidak lagi ikut bicara. Bisa atau tidaknya
voucher diklaim kembali ditentukan hal-hal biasa: masa berlaku, status voucher,
syarat kelayakan member, dan [aturan grup](grup-voucher.md) kalau ada.

### Contoh

Voucher dengan jadwal claim `2026-07-29 00:00:00`:

- Dibuka member pada 28 Juli → tampil, tombol claim mati.
- Dibuka pada 29 Juli pukul 00.00 atau setelahnya → tombol claim hidup.

## Hanya untuk voucher di katalog

Aturan ini **hanya mengunci voucher yang masih di katalog**. Voucher yang sudah
diklaim member sebelumnya tidak pernah ikut terkunci, meskipun salinannya membawa
aturan yang sama dari induknya.

Ini penting karena tanpa pembedaan itu, member yang sudah memegang voucher justru
akan melihat vouchernya sendiri seperti belum boleh diambil — padahal sudah di
tangannya.

## Urutan pemeriksaan

Kalau sebuah voucher punya aturan jadwal claim **dan** aturan grup sekaligus,
pemeriksaan grup dijalankan lebih dulu.

Artinya: member yang sudah mengklaim voucher lain di grup yang sama akan mendapat
pesan *Kamu sudah mengklaim voucher dari grup ini* — bukan pesan *belum bisa
diklaim* — walaupun voucher yang dia coba memang belum sampai jadwalnya. Pesannya
memang yang paling relevan untuk dia: masalahnya bukan waktunya, tapi dia sudah
kehabisan haknya di grup itu.

## Menulis nilainya

Formatnya `YYYY-MM-DD HH:mm:ss`, contohnya `2026-07-29 00:00:00`.

Waktunya dibaca sebagai **jam server**. Kalau server tidak berjalan di WIB,
tuliskan zona waktunya secara eksplisit — `2026-07-29T00:00:00+07:00` juga
diterima — supaya tidak ada selisih jam yang tidak disengaja.

Detail lain soal pengisian nilai ada di [Catatan Operasional](catatan-operasional.md).
