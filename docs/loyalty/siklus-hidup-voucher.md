---
sidebar_position: 2
description: Perjalanan voucher dari terbit sampai ditukar, dan arti tab Aktif & Tidak Aktif yang dilihat member.
---

# Siklus Hidup Voucher

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

Sebelum masuk ke aturan-aturan khusus, ini gambaran perjalanan sebuah voucher.
Empat aturan tambahan yang dibahas di dokumen lain semuanya bekerja dengan cara
**menyela salah satu tahap di bawah ini** — bukan dengan membuat alur baru.

## Empat tahap

### 1. Terbit

Tim OneSmile membuat voucher induk beserta masa berlakunya. Voucher yang sudah
terbit tampil di katalog dan bisa dicari member. Di tahap ini voucher belum
dimiliki siapa pun.

### 2. Diklaim

Member menekan tombol claim. Sistem membuat salinan voucher khusus untuk member
itu — lengkap dengan aturan yang dibawa dari induknya. Voucher induk tetap berada
di katalog untuk member lain.

Sejak titik ini, voucher pindah dari katalog ke daftar "Voucher Saya" milik
member, dan berada di tab **Aktif**.

### 3. Ditukar

Member menunjukkan atau memasukkan kode voucher di tempat berlakunya. Sistem
menandai voucher itu sudah digunakan dan mencatat waktunya. Voucher pindah ke tab
**Tidak Aktif** dengan label "Sudah Digunakan".

### 4. Selesai

Voucher tetap tersimpan sebagai riwayat. Tidak bisa ditukar dua kali.

## Kapan voucher berhenti sendiri

Ada dua cara voucher berakhir tanpa pernah ditukar:

- **Kedaluwarsa** — masa berlakunya lewat sementara voucher masih di tangan member
  dan belum ditukar. Labelnya berubah jadi "Kedaluwarsa" dan voucher pindah ke tab
  Tidak Aktif.
- **Dinonaktifkan** — tim OneSmile mematikan voucher itu. Voucher langsung ke tab
  Tidak Aktif tanpa label khusus.

## Dua tab yang dilihat member

Daftar "Voucher Saya" hanya punya dua tab, dan **setiap voucher selalu berada
tepat di satu tab** — tidak boleh muncul di keduanya, tidak boleh hilang dari
keduanya.

| Tab | Isinya |
| --- | --- |
| **Aktif** | Voucher yang sudah diklaim, belum ditukar, dan masa berlakunya belum lewat. Semuanya diberi label "Aktive". |
| **Tidak Aktif** | Voucher yang sudah ditukar, sudah kedaluwarsa, atau dinonaktifkan. |

Satu-satunya aturan yang bisa mengubah pembagian ini adalah
[masa tenggang setelah dipakai](masa-tenggang-setelah-redeem.md), yang menahan
voucher sudah-ditukar tetap di tab Aktif untuk sementara.

## Tiga penanda di halaman detail

Saat member membuka detail voucher, sistem mengirim tiga penanda yang menentukan
tombol apa yang muncul:

| Penanda | Artinya |
| --- | --- |
| **Bisa diklaim** | Voucher ini masih di katalog dan member boleh mengambilnya. Tombol yang muncul: *Claim*. |
| **Masih berlaku** | Voucher belum kedaluwarsa dan belum ditukar. |
| **Bisa ditukar** | Voucher sudah di tangan member dan siap dipakai. Tombol yang muncul: *Redeem*. |

Kombinasinya mengikuti tahap perjalanan voucher:

| Keadaan voucher | Bisa diklaim | Masih berlaku | Bisa ditukar |
| --- | --- | --- | --- |
| Di katalog, belum diklaim | ✅ | ✅ | ❌ |
| Sudah diklaim, belum ditukar | ❌ | ✅ | ✅ |
| Sudah ditukar | ❌ | ❌ | ❌ |
| Kedaluwarsa | ❌ | ❌ | ❌ |

## Dua lapis penjagaan

Ini pola yang berulang di keempat aturan tambahan, dan penting dipahami supaya
tidak salah menilai saat pengujian.

**Lapis pertama — tampilan.** Voucher yang tidak boleh diambil disembunyikan dari
daftar, atau tombolnya dimatikan. Ini yang membuat aplikasi terasa benar bagi
member.

**Lapis kedua — palang di server.** Saat aplikasi benar-benar mengirim permintaan
claim atau redeem, server memeriksa ulang semua syaratnya dan menolak kalau tidak
memenuhi.

Lapis kedua itulah penegak yang sebenarnya. Lapis pertama sifatnya kosmetik —
member masih bisa sampai ke sebuah voucher lewat tautan share meskipun voucher itu
sudah disembunyikan dari daftarnya. Jadi setiap aturan **selalu** punya palang di
server, bukan hanya penyaringan di tampilan.

Konsekuensinya untuk QA: menemukan voucher yang masih bisa dibuka lewat tautan
share bukan berarti aturannya bocor. Yang perlu diuji adalah apakah tombolnya mati
dan permintaannya ditolak.

## Kalau aturannya salah tulis

Nilai aturan diisi manual oleh tim OneSmile, jadi salah tulis mungkin terjadi.
Prinsip yang dipegang di semua aturan: **nilai yang tidak bisa dibaca akan
diabaikan, bukan membuat voucher terkunci atau daftar voucher gagal tampil.**

Voucher yang aturannya salah tulis akan berperilaku seperti voucher biasa. Ini
disengaja — salah satu karakter tidak boleh sampai membuat voucher tidak pernah
bisa diklaim, atau membuat seluruh daftar voucher milik seorang member gagal
dimuat.
