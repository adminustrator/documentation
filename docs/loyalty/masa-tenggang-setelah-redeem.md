---
sidebar_position: 6
description: Menahan voucher yang sudah ditukar tetap berada di tab Aktif sampai batas waktu tertentu.
---

# Masa Tenggang Setelah Dipakai

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Masalah yang diselesaikan

Voucher yang sudah ditukar normalnya langsung pindah ke tab **Tidak Aktif** dengan
label "Sudah Digunakan". Untuk sebagian voucher itu terlalu cepat.

Voucher parkir contohnya: penukaran terjadi saat kendaraan **masuk**, tapi
vouchernya masih perlu ditunjukkan saat **keluar** — bisa beberapa jam kemudian.
Kalau voucher sudah terkubur di tab Tidak Aktif, member harus mencari-cari di
riwayat sambil menahan antrean di gerbang.

## Yang dilakukan aturan ini

Voucher yang sudah ditukar **ditahan di tab Aktif** sampai batas waktu yang
ditentukan. Setelah batas itu lewat, voucher pindah ke Tidak Aktif seperti biasa.

## Perilaku

Aturan ini **hanya berlaku untuk voucher yang sudah ditukar**. Voucher yang masih
aktif dan belum dipakai tidak tersentuh sama sekali.

| Voucher | Sebelum batas waktu | Setelah batas waktu |
| --- | --- | --- |
| Sudah ditukar, punya masa tenggang | Tab **Aktif** | Tab Tidak Aktif |
| Sudah ditukar, tanpa masa tenggang | Tab Tidak Aktif | Tab Tidak Aktif |
| Belum ditukar | Mengikuti masa berlaku voucher — masa tenggang diabaikan | — |
| Dinonaktifkan | Tab Tidak Aktif | Tab Tidak Aktif |

### Bukan perpanjangan masa berlaku

Ini titik yang paling sering disalahpahami, jadi perlu ditegaskan: **masa tenggang
bukan perpanjangan masa berlaku voucher.**

Voucher yang belum ditukar tetap hangus tepat pada tanggal berakhirnya, seberapa
jauh pun masa tenggangnya diatur. Masa tenggang hanya bicara soal voucher yang
**sudah** ditukar.

Sebaliknya juga berlaku dan justru itu gunanya: voucher yang sudah ditukar tetap
bertahan di tab Aktif selama masa tenggangnya, **walaupun tanggal berakhirnya sudah
lewat**. Untuk parkir sehari ini persis yang dibutuhkan — voucher berlaku satu hari
itu saja, tapi tetap bisa ditunjukkan sampai gerbang keluar malam itu.

### Yang dilihat member selama ditahan

Di daftar "Voucher Saya", voucher ini tampil di tab Aktif dengan label "Aktive" —
sama seperti seluruh isi tab itu.

Kalau member membuka detailnya, halaman detail melaporkan keadaan sejujurnya:
voucher ini tidak bisa diklaim, tidak berlaku lagi, dan tidak bisa ditukar. Yang
memang benar — voucher sudah dipakai, penahanan ini hanya soal penempatan tab
supaya mudah ditemukan.

Voucher parkir tetap menampilkan pelat nomor sebagai kodenya, sesuai
[redeem dengan kendaraan](redeem-dengan-kendaraan.md).

## Satu voucher, satu tab

Yang dijaga ketat dari aturan ini: voucher yang ditahan **tidak boleh muncul di
dua tab sekaligus**, dan tidak boleh hilang dari keduanya.

Kedengarannya sepele, tapi inilah yang paling mudah rusak. Kalau tab Aktif diubah
untuk menampung voucher yang ditahan sementara tab Tidak Aktif tidak ikut
menyingkirkannya, voucher yang sama akan tampil dua kali di dua tempat. Sebaliknya,
voucher tanpa masa tenggang bisa hilang dari kedua tab kalau penyaringannya tidak
lengkap.

Pembagian ini sudah diuji langsung terhadap data voucher sungguhan — termasuk untuk
nilai masa tenggang yang salah tulis — dan tidak ada voucher yang muncul dobel
maupun hilang.

## Menulis nilainya

Formatnya `YYYY-MM-DD HH:MM:SS`, contohnya `2026-07-28 23:59:59`.

Jam boleh tidak ditulis. Kalau hanya tanggal, batasnya dianggap **akhir hari itu**:

| Yang ditulis | Dibaca sebagai |
| --- | --- |
| `2026-07-28 23:59:59` | 28 Juli, pukul 23.59.59 |
| `2026-07-28` | 28 Juli, pukul 23.59.59 |
| `2026-07-29T08:00:00` | 29 Juli, pukul 08.00.00 |
| Format lain | Diabaikan — voucher kembali ke aturan biasa |

:::warning[Berbeda dengan jadwal claim]

Nilai masa tenggang **tidak menerima penulisan zona waktu** seperti `+07:00`.
Sementara [jadwal claim](jadwal-claim.md) menerimanya.

Perbedaannya berasal dari cara kedua nilai itu diperiksa di dalam sistem. Kalau
zona waktu ditulis di sini, nilainya akan dianggap tidak terbaca dan **diabaikan** —
voucher tidak akan ditahan sama sekali.

:::

Perbandingan waktunya memakai jam server aplikasi. Kalau server tidak berjalan di
WIB, perhitungkan selisihnya saat menulis batas waktu.

## Kalau perlu diubah setelah lewat

Voucher yang masa tenggangnya sudah lewat bisa dikembalikan ke tab Aktif dengan
memundurkan batas waktunya ke waktu yang lebih jauh — perubahannya langsung
terasa.

Ini perilaku yang diharapkan, tapi perlu diingat kalau nilainya diubah
serentak untuk banyak voucher: voucher lama yang sudah selesai urusannya bisa
tiba-tiba muncul kembali di tab Aktif member.
