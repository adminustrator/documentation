---
slug: halaman-petugas-parkir-giias
title: Flow Halaman Petugas Parkir GIIAS
authors: [reza]
tags: [announcement, important]
---

# Flow Halaman Petugas Parkir GIIAS

Voucher parkir GIIAS 2026 ditukar member lewat aplikasi, tapi yang berdiri di
gerbang adalah **petugas parkir** — dan petugas tidak punya aplikasi OneSmile.
Untuk itu dibuat satu halaman web sederhana: petugas mengetik plat nomor,
halaman menampilkan voucher parkir yang terdaftar untuk kendaraan itu, lalu
petugas menandai kehadirannya.

Halaman ini dibuka langsung dari browser HP, tanpa login dan tanpa instalasi
apa pun.

<!-- truncate -->

## Alurnya di lapangan

1. **Petugas membuka halaman lewat tautan yang sudah berisi kode aksesnya.**
   Tautan itu dibagikan sekali di awal acara, lalu cukup di-bookmark:

   ```
   https://aca-prd.az-api.onesmile.digital/loyalty/giias/parkir?kode=KODE_PETUGAS
   ```

2. **Halaman memberi tahu dia sedang bertugas sebagai siapa** — misalnya
   *Bertugas sebagai **gate-utara***. Ini penting supaya petugas tahu penandaannya
   akan tercatat atas nama pos mana.

3. **Petugas mengetik plat nomor** kendaraan yang ada di depannya, lalu tekan
   *Cari*. Spasi dan tanda hubung diabaikan, huruf kecil-besar tidak masalah —
   `b 2512 jue`, `B-2512-JUE`, dan `B2512JUE` semuanya menemukan kendaraan yang
   sama.

4. **Hasilnya tampil sebagai daftar kartu**: plat nomor besar di atas, merek dan
   model kendaraan, nama voucher, dan status kehadirannya.

5. **Petugas menekan *Tandai hadir***. Halaman memuat ulang dan menampilkan
   konfirmasi *Kehadiran ditandai.*, dan baris itu berubah jadi **Hadir**
   lengkap dengan jam serta label petugas yang menandainya.

Setelah itu petugas langsung mengetik plat berikutnya — kotak pencarian menempel
di atas layar, jadi tidak perlu scroll balik ke pucuk halaman.

## Dua peran, satu halaman

Halaman ini bisa dibuka siapa saja tanpa login, tapi yang bisa dilakukan berbeda
tergantung ada tidaknya kode akses:

| Yang membuka | Bisa mencari plat | Bisa menandai hadir |
| --- | --- | --- |
| Tanpa kode akses | ✅ | ❌ |
| Dengan kode akses yang benar | ✅ | ✅ |
| Dengan kode akses yang salah | ✅ | ❌ — disertai peringatan |

Pilihan membuat halaman tetap **bisa dibaca tanpa kode** itu disengaja: pengunjung
pameran pun boleh memastikan vouchernya sudah terdaftar untuk kendaraannya, dan
petugas yang kode aksesnya belum sampai tetap bisa membantu mengecek.

Kode yang salah **tidak** membuat halaman diam-diam jadi read-only. Halaman
memberi tahu terus terang bahwa kodenya tidak dikenali — kalau tidak, petugas
akan bingung mencari tombol yang tidak pernah muncul.

## Yang sengaja tidak ditampilkan

Karena halaman ini publik, ada dua hal yang sengaja tidak pernah dikirim ke
halaman:

- **Identitas member** — nama dan nomor HP pemilik voucher tidak ikut. Petugas
  tidak butuh itu; yang dia cocokkan adalah plat kendaraan di depannya.
- **Kode voucher** — kode inilah yang ditukarkan, jadi kalau ikut tampil di
  halaman publik, siapa pun bisa memanennya.

Kode akses petugas juga tidak pernah dicetak ke halaman. Yang ditampilkan hanya
**labelnya** (`gate-utara`), dan label itulah yang tercatat di data kehadiran —
bukan kodenya. Jadi kode petugas tidak ikut tersimpan di tabel voucher yang
dibaca banyak orang.

## Penandaan bersifat sekali dan final

Ini keputusan yang paling menentukan bentuk halamannya: **tidak ada penanda
keluar, dan tidak ada pembatalan.**

Yang dicatat hanya "kendaraan ini hadir, pada jam ini, ditandai oleh pos ini".
Konsekuensinya:

- Tombol *Tandai hadir* **hilang** begitu sebuah voucher ditandai. Tidak ada
  tombol lain yang menggantikannya.
- Kalau petugas menekan tombol dua kali — hal yang sangat mungkin terjadi di HP,
  dengan sinyal seadanya — penandaan kedua tidak menimpa yang pertama. Halaman
  memberi tahu *Voucher ini sudah ditandai hadir sebelumnya — jam kehadirannya
  tidak diubah.* Jam pertama itulah yang otoritatif.
- Menekan refresh setelah menandai tidak mengirim ulang aksinya.

Kalau nanti ternyata butuh koreksi, itu dilakukan dari sisi data, bukan dari
halaman ini. Membuka pembatalan di halaman publik berarti membuka
penyalahgunaannya juga.

## Kenapa halamannya sesederhana ini

Beberapa pilihan teknis yang kelihatan kuno sebenarnya sengaja, dan alasannya
sama: **petugas lapangan memakai HP, dengan sinyal yang tidak bisa diandalkan.**

**Bekerja tanpa JavaScript.** Pencarian dikirim lewat query string biasa dan
penandaan lewat form POST biasa. Tidak ada permintaan latar belakang yang bisa
gagal diam-diam, dan tidak ada layar kosong menunggu skrip termuat.

**Mobile-first, bukan tabel yang dikecilkan.** Di HP tiap voucher jadi satu
kartu; tabel rapat baru muncul di layar lebar. Yang dijaga: di HP halaman **tidak
boleh bisa di-scroll ke samping** — mencari tombol yang kabur ke kanan layar
sambil menahan antrean adalah pengalaman yang buruk.

**Kolom status voucher disembunyikan di HP.** Yang dikerjakan petugas di lapangan
adalah kehadiran, bukan status penukaran voucher. Kolomnya kembali muncul di layar
lebar, untuk yang memantau dari belakang meja.

**Detail kecil yang berdampak besar di lapangan:** ukuran huruf kotak pencarian
dibuat 16px karena di bawah itu Safari iOS otomatis melakukan zoom setiap kali
field disentuh; tombol dibuat setinggi minimal 44px dan selebar kartu supaya
mudah ditekan; nama voucher dibatasi dua baris — bukan satu — karena tanggalnya
ada di ujung nama dan itulah yang membedakan voucher hari Sabtu dari hari Minggu.

Halaman juga mengikuti mode gelap perangkat, dan diberi penanda agar tidak
terindeks mesin pencari.

## Voucher mana yang muncul

Hanya voucher yang **sudah diklaim dan punya kendaraan terdaftar**. Voucher yang
masih di katalog tidak pernah muncul, karena belum ada kendaraan yang menempel
padanya.

Grup vouchernya sendiri diatur lewat konfigurasi, bukan ditulis di kode — jadi
acara tahun berikutnya cukup mengganti nilai konfigurasi tanpa mengubah program.

Sekali tampil dibatasi 50 baris. Kalau hasilnya lebih banyak, halaman
memberi tahu ada berapa totalnya dan meminta pencarian dipersempit. Mengetik plat
lengkap praktis selalu menghasilkan satu baris, jadi batas ini hanya terasa kalau
daftar dibuka tanpa kata pencarian.

Status voucher yang ditampilkan diturunkan dari tanggal-tanggalnya, bukan dari
kode status internal:

| Label | Artinya |
| --- | --- |
| **Belum dipakai** | Voucher masih bisa ditukar |
| **Sudah dipakai** | Sudah ditukar, disertai jam penukarannya |
| **Kedaluwarsa** | Masa berlakunya lewat |

Untuk voucher parkir, batas
[masa tenggang](/docs/loyalty/masa-tenggang-setelah-redeem) dipakai lebih dulu
daripada tanggal berakhir voucher, karena nilainya lebih ketat — aturan yang sama
dengan yang menentukan tab Aktif di aplikasi member.

:::warning[Perlu disiapkan sebelum acara]

Kode akses petugas **tidak punya nilai default**. Selama konfigurasinya belum
diisi, daftar kode kosong, tidak ada kode yang dianggap benar, dan halaman hanya
bisa dibaca. Ini disengaja — halaman yang salah pasang lebih baik tidak bisa
menandai apa pun daripada bisa ditandai siapa pun.

Artinya kode petugas wajib disiapkan lebih dulu bersama tim engineering sebelum
hari pertama pameran, satu kode per pos gerbang supaya jejaknya bisa dibedakan.

:::

## Kaitannya dengan sisi member

Halaman ini pasangan dari alur di aplikasi member. Ringkasnya:

1. Member mengklaim satu voucher parkir dari [grup GIIAS](/docs/loyalty/grup-voucher) —
   satu hari kunjungan saja.
2. Saat masuk, member menukar vouchernya dengan
   [memilih kendaraan](/docs/loyalty/redeem-dengan-kendaraan) yang dipakai. Sejak
   itu, pelat nomor jadi kode vouchernya.
3. Petugas di gerbang mencocokkan pelat itu di halaman ini dan menandai
   kehadirannya.
4. Voucher tetap berada di tab Aktif milik member sampai
   [masa tenggangnya](/docs/loyalty/masa-tenggang-setelah-redeem) lewat, supaya
   masih bisa ditunjukkan di gerbang keluar.

Penjelasan lengkap keempat aturan voucher yang dipakai kampanye ini ada di
[dokumentasi Voucher & Loyalty](/docs/loyalty/intro).
