---
slug: undangan-pemain-tambahan-club-house
title: Undangan Pemain Tambahan di Club House
authors: [reza]
tags: [announcement, important]
---

# Undangan Pemain Tambahan di Club House

Dulu satu booking Club House praktis milik satu orang: yang memesan, dialah yang
datang dan masuk lewat gate. Sekarang sebuah booking bisa membawa **pemain
tambahan** — beberapa residen lain dan beberapa tamu dari luar.

Begitu ada orang lain yang ikut, muncul dua kebutuhan baru. Mereka perlu **tahu**
kapan dan di mana pertandingannya, dan mereka perlu bisa **masuk** lewat gate yang
sama. Undangan pemain tambahan adalah jawaban untuk keduanya.

<!-- truncate -->

## Bagaimana pemain tambahan muncul

Sebelum bicara undangan, ini dulu asal-usul daftar pemainnya.

Pemesan menambahkan layanan "Tambah Residen" atau "Tambah Tamu" pada bookingnya.
Begitu order dibuat, sistem langsung menyiapkan **baris kosong sebanyak jumlah
yang dipesan** — masih bernama sementara seperti *Tambah Residen 1* dan
*Tambah Tamu 1*, belum ada nomor handphonenya.

Pemesan lalu mengisi nama dan nomor handphone tiap pemain, satu per satu. Setelah
lengkap, ia menekan **konfirmasi**. Sejak itu daftar pemain terkunci dan tidak
bisa diubah lagi.

Konfirmasi inilah **pemicu undangan**. Bukan saat order dibuat, bukan saat
pembayaran lunas — undangan baru berjalan setelah pemesan menyatakan daftar
pemainnya sudah benar. Alasannya sederhana: mengirim undangan ke nomor yang masih
salah ketik jauh lebih merepotkan daripada menunggu konfirmasi.

:::info[Kalau ternyata masih perlu diubah]

Kunci itu tidak permanen. Kalau pemesan menambah layanan baru setelah konfirmasi,
form pemain terbuka kembali dan bisa diisi lagi sampai dikonfirmasi ulang. Yang
terkunci hanya selama tidak ada tambahan baru.

:::

Satu hal yang perlu diperhatikan saat mengisi: nama sementara hasil sistem
(*Tambah Residen 1*) **dianggap sudah terisi** karena memang tidak kosong. Jadi
yang biasanya menahan konfirmasi adalah nomor handphone yang belum diisi — bukan
namanya. Kalau konfirmasi ditolak dengan pesan *lengkapi nama & nomor handphone
`{n}` pemain tambahan*, hampir selalu nomornya yang kurang.

## Dua jalur undangan

Setelah dikonfirmasi, undangan berjalan lewat dua jalur berbeda — tergantung
pemainnya residen atau tamu.

| Pemain | Cara diundang |
| --- | --- |
| **Residen yang punya akun OneSmile** | Otomatis dapat push notification berisi undangan, dengan tombol *Lihat Undangan* |
| **Tamu, atau nomor yang tidak terdaftar** | Tidak dapat push. Pemesan membagikan tautan undangannya sendiri lewat WhatsApp atau share sheet |

Isi notifikasinya lengkap dan bisa dibaca tanpa membuka apa pun:

> Anda diundang bermain Tennis 2 oleh REZA NURFACHMI, Senin, 27 Juli 2026 19:00 di
> De Park Club House Tennis 2

Nomor handphone yang diisi pemesan dicocokkan ke akun member yang aktif. Kalau
ketemu dan akunnya punya token notifikasi, undangan terkirim. Kalau tidak — tamu
dari luar, atau residen yang nomornya beda dengan yang terdaftar — tidak ada
notifikasi, dan tautannya harus dibagikan manual.

Satu detail yang disengaja: **kalau satu nomor dipakai beberapa akun member,
semuanya tetap dikirimi.** Lebih baik satu orang menerima dua notifikasi daripada
ada yang tidak menerima sama sekali karena akunnya bukan yang "utama".

## Halaman undangannya

Tiap pemain punya tautan undangannya **sendiri**, dan tautan itu bisa dibuka
siapa saja tanpa login — penting, karena tamu dari luar tidak punya akun OneSmile.

Yang tampil di halaman itu:

- Sapaan dengan namanya sendiri: *Halo Budi Residen, Anda diundang bermain
  Tennis 2*
- Siapa yang mengundang
- Tanggal, waktu, durasi, lokasi, dan alamat
- Perannya — Residen atau Tamu
- Daftar seluruh pemain, dikelompokkan Residen dan Tamu
- **QR akses gate** miliknya sendiri

Halaman ini juga tahu kalau keadaannya sudah berubah. Booking yang dibatalkan
pemesan menampilkan banner *Jadwal ini telah dibatalkan oleh pemesan*, dan
undangan yang jadwalnya sudah lewat menampilkan *Undangan ini sudah tidak
berlaku*. Tautan yang tidak dikenali berujung di halaman *Undangan tidak
ditemukan* — bukan halaman error mentah.

## QR gate per pemain

Ini bagian yang paling menentukan bentuk undangannya.

Di gate, satu booking dihitung sebagai **satu sesi**. Selama sesi masih terbuka,
scan berikutnya ditolak dengan *"Anda sudah melakukan scan masuk."* Artinya kalau
semua pemain memakai QR booking milik pemesan, **hanya orang pertama yang bisa
masuk** — sisanya tertahan di gate.

Karena itu tiap pemain diberi QR-nya sendiri. Isinya sama dengan QR booking biasa,
hanya ditambah penanda pemain mana yang memakainya. Di sisi gate, sesi lalu
dihitung per pemain: satu pemain masuk tidak menghalangi pemain lain di booking
yang sama.

:::note[Pembatas laju di gate]

Alur gate punya pembatas **5 detik** antar-scan — disamakan dengan jalur
aktivitas. Scan yang lebih rapat dari itu dianggap perangkat gate yang mengulang,
bukan orang yang benar-benar menempelkan QR dua kali, dan ditolak dengan
*Terlalu cepat, tunggu beberapa detik.*
[\[Code: 9014\]](/docs/gate/kode-error-scan#kode-club-house-90xx)

Pembatasnya ikut dihitung **per pemain**: satu pemain yang QR-nya terbaca dua
kali tidak menahan pemain berikutnya di booking yang sama. Penolakan ini juga
sengaja tidak mengirim push notif ke member — satu burst retry perangkat gate
akan mengirim beberapa notifikasi "gagal scan" sekaligus untuk kejadian yang
bahkan tidak dilakukan member.

:::

### Kapan QR-nya muncul

QR tidak selalu tampil. Jendelanya **30 menit sebelum sampai 60 menit setelah jam
mulai** — persis sama dengan jendela yang diterima gate.

| Keadaan | Yang tampil di halaman |
| --- | --- |
| Di dalam jendela gate | Blok *QR Akses Gate* beserta gambar QR-nya |
| Belum masuk jendela | Catatan *QR akses gate akan muncul mulai pukul 18:30* |
| Booking dibatalkan atau jadwal lewat | Banner pembatalan/kedaluwarsa, tanpa QR |

Penyamaan jendela ini disengaja: kalau QR tampil di waktu yang pasti ditolak
gate, pemain akan berdiri di gate mencoba memindai sesuatu yang memang tidak akan
diterima. Lebih baik QR-nya belum ada, disertai jam kemunculannya.

## Yang dijaga karena tautannya publik

Tautan undangan bisa dibuka tanpa login, dan bisa diteruskan orang. Itu memang
konsekuensi yang diterima — tamu dari luar tidak punya akun. Yang dilakukan
sebagai gantinya adalah membatasi kerugian kalau tautan itu jatuh ke tangan lain.

**Tautannya tidak bisa ditebak.** Token undangan dibuat acak, bukan diturunkan
dari nomor urut pemain. Jadi tidak ada yang bisa menjelajahi undangan orang lain
dengan menaikkan angka satu per satu.

**Isinya sengaja miskin data.** Halaman undangan tidak memuat nomor handphone
siapa pun, tidak memuat harga, tidak memuat kode tagihan, dan tidak memuat
tautan pemain lain. Yang tampil hanya yang memang perlu diketahui orang yang
diundang.

**QR-nya tidak pernah keluar dari halaman.** QR hanya dirender di halaman
undangan, tidak pernah dikirim lewat jalur data yang bisa diakses publik. Yang
bisa diketahui dari luar halaman hanya *apakah* QR-nya sudah waktunya muncul, dan
mulai jam berapa.

**Token tidak ikut tercatat di log.** Setiap permintaan ke server tercatat
lengkap dengan alamat yang dibuka. Kalau dibiarkan, seluruh token undangan akan
tersimpan di tabel log dan bisa dibaca siapa pun yang punya akses ke sana. Karena
itu token disamarkan menjadi `/invite/***` sebelum dicatat.

**Penjaga terakhirnya ada di gate.** Karena tautan bisa diteruskan, validasi di
gate yang menjadi batas sebenarnya: satu QR pemain = satu sesi. Jadi satu tautan
yang tersebar tetap tidak bisa memasukkan banyak orang sekaligus.

Halaman undangan juga tidak diindeks mesin pencari, dan footernya mengingatkan
bahwa tautan itu bersifat pribadi.

## Pengingat satu jam sebelum bermain

Selain undangan, konfirmasi pemain juga menulis **pengingat pertandingan** yang
dikirim satu jam sebelum jadwal:

> Pertandingan Tennis 2 Senin, 27 Juli 2026 19:00 — 1 jam lagi di De Park Club
> House Tennis 2

Penerimanya pemesan **dan** seluruh pemain residen yang nomornya cocok ke akun
member. Pemesan yang juga mendaftarkan dirinya sebagai pemain hanya menerima satu
pengingat, tidak dobel.

Pengingat ini mengikuti perubahan jadwal: booking yang dibatalkan membuat
pengingatnya dibatalkan juga, dan booking yang dijadwalkan ulang membuat
pengingatnya ditulis ulang dengan waktu baru. Booking yang jadwalnya sudah lewat
atau sudah dibatalkan tidak menjadwalkan apa pun.

## Ruang penyempurnaan

Cakupan rilis ini sengaja dijaga pada alur utamanya: residen dan tamu diundang,
membuka undangannya, dan masuk lewat gate dengan sesinya masing-masing. Beberapa
hal di sekitarnya sudah diidentifikasi dan menunggu keputusan lanjutan.

- **Pemesan yang mendaftarkan nomornya sendiri sebagai pemain residen** ikut
  menerima undangan atas namanya sendiri. Tidak mengganggu jalannya undangan, dan
  bisa dirapikan kapan saja bila dirasa perlu.
- **Lokasi yang memakai jalur sinkronisasi kode gate terpisah** — Eonna, misalnya
  — masih mencatat satu entri per booking. Fitur pemain tambahan bisa diperluas ke
  sana, tapi itu keputusan tersendiri karena menyentuh cara lokasi tersebut
  bertukar data.
- **Kedatangan tamu tanpa pemesan** saat ini tidak dibedakan: tamu bisa masuk
  selama QR-nya valid dan berada di jendela waktunya. Apakah perlu dibedakan lebih
  merupakan pertanyaan kebijakan operasional daripada teknis.

## Ringkasnya

1. Pemesan menambahkan layanan pemain tambahan → sistem menyiapkan barisnya.
2. Pemesan mengisi nama dan nomor tiap pemain, lalu **konfirmasi**.
3. Residen ber-akun dapat push notification; tamu dapat tautan yang dibagikan
   pemesan.
4. Tiap pemain membuka halaman undangannya sendiri — lengkap dengan jadwal,
   daftar pemain, dan QR gate miliknya.
5. QR muncul 30 menit sebelum jadwal, dan tiap pemain masuk dengan sesinya
   sendiri.
6. Satu jam sebelum bermain, pemesan dan pemain residen dapat pengingat.

Untuk konteks Club House secara umum, lihat
[Implementasi Club House The ZORA](/blog/zora-club-house).
