---
sidebar_position: 4
description: Beberapa voucher dirilis sebagai satu paket pilihan — member hanya boleh mengambil satu.
---

# Grup Voucher

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Masalah yang diselesaikan

Kampanye parkir GIIAS 2026 terbit sebagai empat voucher terpisah — satu untuk
tiap tanggal pameran. Member seharusnya **memilih satu hari kunjungan**, bukan
mengumpulkan keempatnya.

Membatasi kuota per voucher tidak menyelesaikan ini: member tetap bisa mengambil
keempat voucher, satu per satu. Yang dibutuhkan adalah batas yang berlaku
**lintas voucher**.

## Yang dilakukan aturan ini

Beberapa voucher diberi nama grup yang sama. Member boleh mengklaim satu di antara
mereka; begitu satu diambil, sisanya tertutup.

## Perilaku

| Keadaan | Yang terjadi |
| --- | --- |
| Belum mengambil apa pun di grup | Semua voucher grup tampil dan bisa diklaim |
| Sudah mengambil salah satu | **Seluruh voucher grup hilang dari katalog** — termasuk yang dia ambil |
| Voucher yang sudah dia ambil | Tetap ada di "Voucher Saya" dan tetap bisa ditemukan lewat pencarian |
| Mencoba claim lagi di grup itu | Ditolak: *Kamu sudah mengklaim voucher dari grup ini* |
| Membuka detail voucher grup di katalog | Detail tetap terbuka, tombol claim mati, disertai keterangan alasannya |
| Membuka detail vouchernya sendiri | Tidak terpengaruh aturan grup |

Voucher tanpa nama grup tidak pernah tersentuh aturan ini.

### Kenapa yang dia ambil juga hilang dari katalog

Ini pilihan yang disengaja dan kadang mengagetkan saat pengujian. Setelah member
mengambil voucher hari Sabtu, voucher hari Sabtu itu pun hilang dari katalog untuk
dia — bukan hanya tiga hari lainnya.

Alasannya: voucher itu sudah ada di "Voucher Saya". Membiarkannya tetap tampil di
katalog dengan tombol mati hanya menimbulkan pertanyaan "kenapa saya tidak boleh
ambil voucher yang sudah saya punya?". Lebih bersih kalau seluruh grup selesai
urusannya begitu member sudah memilih.

Konsekuensinya, mencoba mengklaim ulang voucher **yang sama** juga ditolak dengan
pesan yang sama.

## Apa yang dihitung sebagai "sudah mengambil"

Hanya voucher yang **masih aktif** atau **sudah digunakan** yang menghalangi
pengambilan baru. Voucher grup yang sudah kedaluwarsa atau dinonaktifkan tidak
menghalangi — member berhak mengambil lagi dari grup itu.

## Penyembunyian menyeluruh

Voucher grup yang tertutup hilang dari **seluruh permukaan katalog**, bukan hanya
dari satu daftar:

- daftar voucher
- pencarian voucher
- jumlah voucher pada badge per kategori

Yang terakhir ini mudah terlewat: kalau badge kategori masih menghitung voucher
yang sudah disembunyikan, member melihat angka "4 voucher" lalu membuka kategori
itu dan menemukannya kosong.

Sementara di sisi "Voucher Saya", voucher milik member sendiri **tidak pernah
ikut hilang** — termasuk saat dicari lewat pencarian. Pencarian punya dua sisi:
mencari di katalog (kena aturan grup) dan mencari di koleksi member sendiri (tidak
kena).

## Keterangan di halaman detail

Kalau member membuka detail voucher grup yang sudah tertutup, halaman tetap
terbuka lengkap — hanya tombolnya mati, disertai keterangan *Kamu sudah mengklaim
voucher dari grup ini*.

Sengaja tidak dibuat halaman error, karena member mungkin sampai ke halaman itu
dari tautan yang dibagikan orang lain dan tetap berhak melihat isi vouchernya.

## Menulis nilainya

Nama grup ditulis sama persis di semua voucher yang mau diikat, contohnya
`GIIAS 2026`. Yang mengikat mereka hanyalah kesamaan nama itu — tidak ada daftar
grup terpisah yang perlu dibuat lebih dulu.

Karena itu **beda satu karakter berarti grup yang berbeda**. Lihat
[Catatan Operasional](catatan-operasional.md) untuk hal-hal yang perlu diperiksa
saat menyiapkan sebuah grup.
