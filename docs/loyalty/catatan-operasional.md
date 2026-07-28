---
sidebar_position: 7
description: Ringkasan keempat aturan, cara menuliskannya, dan jebakan yang sudah pernah ditemui.
---

# Catatan Operasional

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

Halaman ini untuk yang menyiapkan data voucher dan yang mengujinya sebelum
kampanye jalan.

## Ringkasan keempat aturan

| Aturan | Berlaku pada | Contoh nilai | Zona waktu |
| --- | --- | --- | --- |
| [Jadwal claim](jadwal-claim.md) | Voucher di katalog | `2026-07-29 00:00:00` | Boleh ditulis |
| [Grup voucher](grup-voucher.md) | Voucher di katalog | `GIIAS 2026` | — |
| [Redeem dengan kendaraan](redeem-dengan-kendaraan.md) | Saat penukaran | `vehicle` | — |
| [Masa tenggang](masa-tenggang-setelah-redeem.md) | Voucher yang sudah ditukar | `2026-08-01 23:59:59` | **Tidak boleh** |

Keempatnya boleh dipasang bersamaan di satu voucher dan tidak saling mengganggu.
Voucher parkir GIIAS 2026 memakai empat-empatnya: masuk ke satu grup, baru boleh
diambil pada tanggal tertentu, ditukar dengan memilih kendaraan, dan tetap di tab
Aktif sampai malam hari kunjungan.

## Prinsip menulis nilai

**Salah tulis berarti aturan diabaikan, bukan voucher terkunci.** Voucher yang
nilainya tidak bisa dibaca akan berperilaku seperti voucher biasa — bukan menjadi
tidak bisa diklaim selamanya, dan bukan membuat daftar voucher gagal tampil.

Ini melindungi dari kesalahan kecil, tapi juga berarti **salah tulis tidak
menimbulkan pesan error apa pun**. Voucher yang seharusnya terjadwal justru
langsung bisa diklaim, dan tidak ada yang memberi tahu. Karena itu nilai yang
sudah diisi perlu diperiksa langsung di aplikasi, bukan diasumsikan bekerja.

Cara memeriksa paling cepat: buka detail voucher di aplikasi dan lihat nilai
aturannya ikut terkirim apa adanya. Kalau nilainya tampil tapi perilakunya tidak
berubah, hampir selalu formatnya yang salah.

## Jebakan yang sudah pernah ditemui

### Masa berlaku lebih awal dari tanggal kegunaannya

Empat voucher parkir GIIAS 2026 dibuat untuk tanggal 1, 2, 8, dan 9 Agustus 2026,
tetapi keempatnya diberi tanggal berakhir **31 Juli 2026** — lebih awal dari hari
berlakunya sendiri.

Akibatnya voucher untuk tanggal 8 dan 9 Agustus akan terhitung kedaluwarsa sebelum
harinya tiba, dan tidak akan bisa dipakai member. Tanggal berakhir masing-masing
perlu diset ke hari berlakunya sebelum kampanye dijalankan.

:::warning

Periksa ini setiap kali menyiapkan kampanye berbasis tanggal kunjungan: **tanggal
berakhir voucher tidak boleh lebih awal dari hari voucher itu dipakai.**

:::

### Nama grup yang beda satu karakter

Voucher diikat menjadi satu grup hanya oleh kesamaan namanya. `GIIAS 2026` dan
`GIIAS  2026` (dua spasi) adalah dua grup berbeda, dan member akan bisa mengambil
satu voucher dari masing-masing.

Saat menyiapkan grup, periksa nama grup seluruh anggotanya sama persis — termasuk
spasi dan besar-kecil huruf.

### Voucher yang diklaim sebelum aturan ini ada

Voucher yang sudah diklaim member **sebelum** penyalinan aturan ke salinan member
diaktifkan tidak membawa aturan apa pun. Untuk aturan grup, artinya member itu
tetap melihat seluruh voucher grup di katalog seolah belum pernah mengambil apa
pun.

Untuk kampanye GIIAS hal ini tidak jadi masalah karena voucher-vouchernya belum
pernah diklaim siapa pun. Untuk kampanye lain yang sudah berjalan lebih dulu, data
lama perlu disesuaikan lebih dulu — koordinasikan dengan tim engineering.

### Zona waktu yang tidak seragam

Jadwal claim menerima penulisan zona waktu (`+07:00`), masa tenggang tidak. Kalau
kebiasaan menulis dari satu aturan dibawa ke aturan lain, masa tenggangnya akan
diabaikan tanpa peringatan — voucher parkir langsung pindah ke tab Tidak Aktif
begitu ditukar.

## Daftar periksa sebelum kampanye jalan

- [ ] Tanggal berakhir setiap voucher **tidak lebih awal** dari hari voucher itu dipakai.
- [ ] Nama grup sama persis di seluruh anggota grup.
- [ ] Jadwal claim jatuh sebelum hari kegunaan voucher.
- [ ] Masa tenggang ditulis **tanpa** zona waktu.
- [ ] Untuk voucher berkendaraan: sudah dicoba oleh akun uji yang **belum** punya kendaraan terdaftar — tombol redeem harus tertahan, bukan error.
- [ ] Untuk voucher grup: setelah akun uji mengambil satu voucher, seluruh grup hilang dari katalog **dan** badge jumlah per kategori ikut turun.
- [ ] Voucher milik akun uji masih ketemu lewat pencarian setelah grupnya tertutup.

## Catatan performa

Dua hal yang belum dioptimalkan, dicatat di sini supaya tidak jadi kejutan:

- **Penyaringan grup voucher** dijalankan untuk tiap voucher kandidat, dan
  perhitungan badge per kategori memanggilnya sekali per kategori. Kalau jumlah
  voucher di produksi sudah besar dan daftar voucher terasa lambat, ini kandidat
  pertama yang perlu diperiksa.
- **Masa tenggang** tidak punya masalah serupa, karena penyaringannya selalu
  dibatasi ke voucher milik satu member lebih dulu — jumlah barisnya kecil kecuali
  ada member dengan ribuan voucher.

Keduanya bisa dipercepat tim engineering tanpa mengubah perilaku yang dijelaskan
di dokumen-dokumen ini.
