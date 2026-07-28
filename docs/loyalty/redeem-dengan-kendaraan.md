---
sidebar_position: 5
description: Voucher yang penukarannya wajib mencatat kendaraan member yang dipakai.
---

# Redeem dengan Kendaraan

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Masalah yang diselesaikan

Voucher parkir tidak cukup ditukar begitu saja — petugas di gerbang perlu tahu
**kendaraan mana** yang dibebaskan biayanya. Satu member bisa punya beberapa
kendaraan terdaftar, dan hanya satu di antaranya yang dipakai hari itu.

Jadi penukaran voucher jenis ini harus sekaligus mencatat kendaraannya, dan
catatan itu harus bisa ditunjukkan kembali setelahnya.

## Yang dilakukan aturan ini

Voucher ditandai "ditukar dengan kendaraan". Saat member menekan redeem, aplikasi
tidak langsung menukar — aplikasi lebih dulu meminta member memilih salah satu
kendaraan terdaftarnya, lalu mengirim pilihan itu bersama penukaran.

## Perjalanan member

1. Member membuka detail voucher.
2. Aplikasi melihat voucher ini menuntut kendaraan, dan menampilkan daftar
   kendaraan terdaftar milik member — sudah dalam bentuk siap dipilih, misalnya
   *Honda Brio - B2512JUE*.
3. Member memilih satu kendaraan.
4. Penukaran dijalankan seperti biasa, dengan kendaraan pilihan itu ikut terkirim.
5. Setelah berhasil, **pelat nomor kendaraan itu yang ditampilkan sebagai kode
   vouchernya** — inilah yang ditunjukkan ke petugas.

## Kalau member belum punya kendaraan terdaftar

Aplikasi menahan tombol redeem dan mengarahkan member mendaftarkan kendaraannya
lebih dulu. Voucher tidak hilang dan tidak hangus — member tinggal kembali setelah
kendaraannya terdaftar.

Ini situasi yang wajar, bukan kesalahan: member bisa saja mengklaim voucher parkir
sebelum sempat mendaftarkan kendaraan.

## Kendaraan mana yang bisa dipilih

Hanya kendaraan yang **masih aktif** di profil member. Kendaraan yang sudah
dihapus member tidak ikut ditawarkan.

## Yang ditolak server

Palang di server memeriksa dua hal, dan **penolakan terjadi sebelum voucher
disentuh sama sekali** — status voucher tidak berubah, kodenya tidak berubah,
tidak ada catatan atau notifikasi yang dibuat. Member bisa mencoba lagi dengan
aman.

| Kondisi | Pesan |
| --- | --- |
| Kendaraan tidak dipilih | *Kendaraan wajib dipilih* |
| Kendaraan yang dikirim bukan milik member, atau sudah dihapus | *Kendaraan tidak dikenal* |
| Semua syarat terpenuhi | *Sukses menggunakan voucher.* |

Voucher yang tidak menuntut kendaraan sama sekali tidak berubah perilakunya oleh
palang ini.

## Setelah ditukar: pelat nomor jadi kodenya

Voucher parkir yang sudah ditukar menampilkan **pelat kendaraan yang dipakai**
sebagai kode vouchernya, lengkap dengan tombol salin:

> Status: Sudah Digunakan
> Kode: **B2512JUE**

Tanpa aturan ini, yang tampil adalah kode internal hasil penukaran yang panjang dan
tidak berarti bagi siapa pun — atau kodenya malah tidak ditampilkan sama sekali.

Dua hal yang dijaga di sini:

- **Pelatnya tetap tampil walaupun kendaraannya sudah dihapus member.** Penukaran
  itu memang terjadi dengan kendaraan tersebut; riwayatnya tidak boleh berubah
  hanya karena member membereskan daftar kendaraannya.
- **Kalau kendaraannya benar-benar tidak bisa ditemukan lagi**, kode voucher
  dibiarkan seperti apa adanya — tidak dikosongkan. Lebih baik menampilkan kode
  yang kurang berguna daripada halaman detail yang kosong.

Penggantian kode dengan pelat ini hanya berlaku di halaman detail voucher. Daftar
"Voucher Saya" memang tidak menampilkan kode voucher, jadi tidak ada yang berubah
di sana.

## Pasangan alaminya: masa tenggang

Voucher parkir hampir selalu dipasangkan dengan
[masa tenggang setelah dipakai](masa-tenggang-setelah-redeem.md). Alasannya
praktis: parkir ditukar saat **masuk**, tapi pelatnya masih perlu ditunjukkan saat
**keluar**. Tanpa masa tenggang, voucher sudah pindah ke tab Tidak Aktif sebelum
member sampai ke gerbang keluar.
