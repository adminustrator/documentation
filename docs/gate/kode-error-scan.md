---
sidebar_position: 1
description: Arti setiap kode error yang muncul saat QR atau wajah dipindai di gate, penyebabnya, dan siapa yang bisa menindaklanjutinya.
---

# Kode Error Scan di Gate

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

Setiap kali QR ditempel atau wajah dipindai di gate, perangkat gate memanggil satu
endpoint di OneSmile dan menampilkan apa pun yang dibalas endpoint itu ke layar
kecil di gate. Kalau scan ditolak, yang terbaca petugas dan member bukan
"gagal" saja — melainkan kalimat berbahasa Indonesia yang diakhiri kode dalam
kurung siku, misalnya:

> Gate tidak dikenal. **[Code: 9007]**

Halaman ini adalah kamus kode tersebut: apa artinya, kapan muncul, dan siapa yang
sebenarnya bisa memperbaikinya.

## Endpointnya

| | |
| --- | --- |
| Endpoint | `POST /general-api/api/v2/gate/scan` |
| Base URL produksi | `https://aca-prd.az-api.onesmile.digital/general-api` |
| Autentikasi | Header `Authorization: Basic <key>` |

Bentuk balasannya selalu sama, sukses maupun gagal:

```json
{
  "success": false,
  "msg": "Gate tidak dikenal. [Code: 9007]",
  "data": {
    "msg": "Gate tidak dikenal. [Code: 9007]"
  }
}
```

:::warning[Baca `msg` yang di level atas]

`data` tidak konsisten bentuknya — kadang objek `{ msg }`, kadang objek yang juga
memuat `appointment_id`, kadang **array kosong** `[]`. Satu-satunya field yang
dijamin ada di semua jalur adalah `msg` di level atas. Perangkat gate sebaiknya
menampilkan itu, bukan `data.msg`.

:::

Status HTTP-nya mengikuti jenis penolakannya:

| Status | Artinya |
| --- | --- |
| `200` | Scan diterima, gate boleh dibuka (`success: true`) |
| `400` | Ditolak karena aturan bisnis — datang terlalu awal, sudah scan, salah hari |
| `403` | Ditolak sebelum masuk logika bisnis — kredensial salah, atau QR tidak cocok ke pesanan mana pun |
| `500` | Gangguan sistem, bukan kesalahan member (lihat [Gangguan sistem](#gangguan-sistem)) |

## Cara membaca nomornya

- **90xx** — jalur **Club House**: QR booking, QR pemain tambahan, face
  recognition, dan tamu.
- **91xx** — jalur **Aktivitas/Facility**: booking fasilitas seperti Vanya Park.
- Akhiran **`F`** (mis. `9007F`, `9012F`) menandai kejadian yang sama tetapi
  datang dari jalur **face recognition**, bukan QR. Ini yang membedakan "gate
  tidak dikenal saat scan QR" dari "gate tidak dikenal saat scan wajah" di log.
- Sebagian penolakan **tidak berkode** — kalimatnya sudah menjelaskan dirinya
  sendiri. Daftarnya ada di [Penolakan tanpa kode](#penolakan-tanpa-kode).

## Kode Club House (90xx)

| Kode | Pesan di layar gate | Penyebab | Tindak lanjut |
| --- | --- | --- | --- |
| **9003** | QR Clubhouse tidak valid. | Kode booking di dalam QR tidak ketemu untuk lokasi yang diminta QR itu. Umumnya QR dari lokasi lain, atau bookingnya sudah dihapus. Dibalas `403`. | Cek booking member di panel; pastikan lokasi di QR sama dengan lokasi bookingnya. |
| **9005** | QR telah kedaluwarsa. | QR pendek (bukan QR terenkripsi) tidak cocok ke satu pun: appointment hari ini, wajah terdaftar, booking club house, atau kartu member. Ini balasan jatuh-akhir untuk QR yang tidak dikenali sama sekali. | Minta member memuat ulang QR dari aplikasi. Kalau berulang, kirim isi tokennya ke tim engineering. |
| **9007** | Gate tidak dikenal. | Dua kemungkinan: (1) `gate_id` + cluster member + tipe gate (IN/OUT) tidak terdaftar aktif di pemetaan gate; (2) gate terdaftar, tapi **location group**-nya beda dengan booking — mis. QR untuk Sportivo ditempel di gate Eonna. | Pengelola gate + engineering: cek `gate_mapping` (`gate_id`, `cluster_id`, `gate_type`, `status = 1`, dan `notes` yang harus berformat `Club House <nama location group>`). |
| **9007F** | Gate tidak dikenal. | Sama seperti 9007, tapi dari scan wajah: gate yang dipindai tidak punya `notes` berformat `Club House …`, jadi location group-nya tidak bisa ditentukan. | Sama seperti 9007. |
| **9008** | User tidak dikenal. | Booking ketemu, tapi `member_id`-nya tidak ada di tabel member. Data tidak konsisten. | Engineering — bookingnya menggantung tanpa pemilik. |
| **9009** | Hanya bisa scan di hari yang ditentukan. | Tidak ada booking milik kode itu **untuk hari ini** dengan status yang boleh masuk. Termasuk booking yang dibatalkan. | Member: pastikan tanggal bookingnya memang hari ini. |
| **9010** | Gagal scan keluar. | Kombinasi log kunjungan yang tidak terduga saat scan OUT. Praktis tidak pernah tercapai lewat alur normal. | Engineering. |
| **9011** | Gagal scan masuk. | Kombinasi log kunjungan yang tidak terduga saat scan IN. Sama seperti 9010. | Engineering. |
| **9012** | Action tidak dikenal. | `gate` bukan `IN`/`OUT`, atau `gate_id` tidak dikirim. | Perangkat/integrator gate: perbaiki body request. |
| **9012F** | Action tidak dikenal. | Sama seperti 9012 pada jalur face recognition. | Sama seperti 9012. |
| **9013** | QR pemain tidak sesuai booking. | QR memuat `player_id`, tapi pemain itu tidak ada atau miliknya booking lain. Ini pengaman [QR pemain tambahan](/blog/undangan-pemain-tambahan-club-house). | Member: buka ulang tautan undangannya. Kalau tetap, engineering. |
| **9014** | Terlalu cepat, tunggu beberapa detik. | Dua scan pada booking/pemain yang sama berjarak kurang dari **5 detik**. Dianggap perangkat gate yang mengulang, bukan orang yang benar-benar menempel dua kali. | Tunggu sebentar lalu ulangi. Tidak perlu tindak lanjut. |
| **9022** | Action tidak dikenal. | Jalur **tamu** club house: tamu punya izin aktif, tapi tidak ada gate yang cocok untuk izin itu (gate, cluster, tipe IN/OUT, dan nama location group harus cocok semua) — atau `gate` bukan IN/OUT. | Cek izin tamu di panel: apakah location group-nya memang mencakup gate ini. |
| **9028** | Tamu tidak dikenal. | Tidak ada izin tamu aktif untuk member ini: hari ini di luar `access_days`, atau di luar rentang tanggal berlakunya. | Member/pengelola: perpanjang atau perbaiki izin tamunya. |

:::note[9014 tidak dianggap kesalahan member]

Penolakan 9014 satu-satunya yang ditandai **silent**: member tidak dikirimi push
notif "gagal scan". Alasannya, satu burst retry perangkat gate akan mengirim
beberapa notifikasi sekaligus untuk kejadian yang bahkan tidak dilakukan member.

:::

## Kode Aktivitas / Facility (91xx)

| Kode | Pesan di layar gate | Penyebab | Tindak lanjut |
| --- | --- | --- | --- |
| **9100** | Not Ok | Gate terdaftar dan aktif, tapi `gate` bukan `IN`/`OUT` atau `gate_id` kosong. Ini pesan bawaan jalur facility yang tersisa saat tidak ada cabang lain yang mengisi pesannya. | Perangkat/integrator gate: perbaiki body request. |
| **9103** | QR Aktivitas tidak valid. | Tidak ada booking facility dengan kombinasi member + kode aktivitas di QR. Dibalas `403`. | Cek booking aktivitas member di panel. |
| **9107** | Gate tidak dikenal. | Tidak ada pemetaan gate aktif dengan `gate_id` itu **dan** `notes` sama dengan `client_type` bookingnya (mis. `FACILITY-VANYAPARK`). | Engineering/pengelola gate: cocokkan `notes` di `gate_mapping` dengan `client_type` fasilitasnya. |
| **9109** | Hanya bisa scan di hari yang ditentukan. | `picked_date` booking bukan hari ini (WIB). | Member: datang di tanggal bookingnya. |
| **9112** | Action tidak dikenal. | Nilai awal internal. **Tidak pernah sampai ke layar gate** — pesan akhirnya selalu ditimpa, dan untuk kasus ini yang muncul adalah 9100. Didaftarkan di sini supaya tidak dikira hilang saat ditelusuri di kode. | — |
| **9113** | Gagal menyimpan data gate. Silakan hubungi petugas. | Transaksi penyimpanan sesi gate gagal. Penyebab paling sering: kolom `inside_count` belum ada di tabel `facility_member` — tanpa kolom itu **semua** scan facility (IN maupun OUT) berbalas kode ini. | **Engineering, segera.** Ini kegagalan sistem, bukan kesalahan member. |
| **9114** | Membership Vanya Park tidak ditemukan atau belum aktif. | Booking Vanya Park tanpa baris membership aktif milik member itu, atau membershipnya belum diklaim. Hanya diperiksa saat scan **masuk**. | Cek membership member di panel. |
| **9115** | Membership Vanya Park sudah berakhir pada dd-mm-yyyy. | Membership ada, tapi tanggal berakhirnya sudah lewat. | Member: perpanjang membership. |
| **9116** | Semua tamu pada pesanan ini sudah masuk (N orang). | Anti-passback: jumlah orang yang sudah masuk memakai booking ini sudah mencapai `total_customer`. Hanya menolak bila anti-passback berjalan pada mode `enforce`. | Sesuai desain. Kalau rombongannya memang lebih banyak, `total_customer` bookingnya yang perlu dikoreksi. |
| **9117** | Terlalu cepat, tunggu beberapa detik. | Dua scan searah berjarak kurang dari **5 detik** (`FACILITY_ANTIPASSBACK_THROTTLE_SECONDS`). Dianggap retry perangkat. Hanya menolak pada mode `enforce`. | Tunggu sebentar lalu ulangi. |

:::info[Tiga mode anti-passback]

Jalur facility punya saklar `FACILITY_ANTIPASSBACK_MODE`:

| Mode | Perilaku |
| --- | --- |
| `off` | Okupansi tidak dihitung sama sekali; 9116 dan 9117 tidak pernah muncul. |
| `shadow` *(default)* | Okupansi tetap dihitung dan pelanggarannya dicatat, **tapi gate tetap dibuka**. |
| `enforce` | Pelanggaran menolak scan masuk — di sinilah 9116 dan 9117 benar-benar terasa. |

Jadi kalau 9116/9117 tidak pernah terlihat di lapangan, itu bukan berarti tidak
ada pelanggaran — cek dulu modenya.

:::

## Penolakan tanpa kode

Kalimat-kalimat ini muncul tanpa `[Code: …]`. Semuanya dibalas `400`, dan di
pelaporan internal tercatat dengan kode `UNHANDLED`.

| Pesan | Jalur | Artinya |
| --- | --- | --- |
| Anda sudah melakukan scan masuk. | Club house | Sesinya masih terbuka — orang ini sudah tercatat di dalam dan belum scan keluar. Gate tidak boleh dibuka dua kali untuk satu sesi. |
| Anda sudah melakukan scan keluar. | Club house | Sesi terakhir sudah ditutup. |
| Hanya bisa scan di mulai dari 30 menit sebelum dari waktu yang ditentukan. | Club house & facility | Datang di luar jendela waktu: **30 menit sebelum** sampai **60 menit sesudah** jam mulai booking. |
| Jam main sudah berakhir, tidak bisa scan masuk lagi. | Club house (pemain tambahan) | Pemain tambahan boleh keluar-masuk sepanjang jam main, tapi batas scan masuknya adalah **jam selesai slot**, bukan jam mulai + 60 menit. |
| Tidak ditemukan pesanan untuk gate ini hari ini. | Club house (face recognition) | Wajah dikenali, tapi member itu tidak punya booking hari ini di location group tempat gate berada. |
| Status pesanan tidak aktif. | Facility | Booking ada di hari yang benar, tapi statusnya bukan aktif. |
| Gate tidak aktif. Segera pinta pengelola Automated Gate untuk menghubungi OneSmile. | Facility | Gate terdaftar tapi dimatikan (`status = 0`). |

## Gagal sebelum masuk logika bisnis {#gangguan-sistem}

| Pesan | HTTP | Kode internal | Artinya |
| --- | --- | --- | --- |
| Unauthorized | `403` | `AUTH_FORBIDDEN` | Request tidak membawa header `Authorization`. |
| Token mismatched | `403` | `AUTH_FORBIDDEN` | Header `Authorization` ada tapi isinya salah. |
| Terjadi gangguan pada sistem gate. Silakan hubungi petugas. | `500` | `EXCEPTION` | Kegagalan tak terduga di sisi server. Detail teknis dan stack trace-nya masuk ke tabel log internal (`BOOMGATES_ERROR`), **tidak** ikut dikirim ke gate maupun ke aplikasi member. |

Pesan `500` ini sengaja dibedakan dari penolakan `400`. Tanpa itu, exception
terkirim sebagai `400` dan ikut terhitung sebagai penolakan bisnis biasa —
gangguan sistem jadi tersamar di antara member yang datang kesiangan.

## Yang terjadi setelah scan ditolak

Satu penolakan meninggalkan tiga jejak sekaligus.

**1. Push notification ke member.** Kegagalan scan club house mengirim notifikasi
*Gagal Masuk Club House* / *Gagal Keluar Club House* berisi alasannya persis
seperti yang tampil di gate. Pengecualiannya throttle (9014), yang sengaja
dibungkam. Notifikasi ini tidak pernah boleh menggagalkan alur gate — errornya
ditelan.

**2. Dokumen error di Firestore** untuk di-listen aplikasi member. Setiap respons
`success: false` di endpoint scan ditulis ke koleksi `gate_scan_errors` (dapat
diganti lewat `FIRESTORE_ERROR_COLLECTION`), berumur **7 hari**
(`GATE_ERROR_TTL_DAYS`). Isinya, antara lain:

| Field | Keterangan |
| --- | --- |
| `code` | Kode dari pesan (`9007`), atau `AUTH_FORBIDDEN` / `EXCEPTION` / `UNHANDLED` bila pesannya tidak berkode |
| `severity` | `business`, `auth`, atau `system` |
| `msg`, `http_status`, `gate_id`, `gate` | Persis yang dibalas ke gate |
| `member_id`, `club_house_code`, `facility_code`, `location_id` | Konteks, diisi sejauh yang berhasil ditelusuri |
| `token_fingerprint` | **Sidik jari** SHA-256 token, bukan tokennya — QR masih bisa dipakai ulang, jadi isinya tidak boleh tersimpan |
| `request_id` | Sama dengan header `X-Request-Id` pada respons; ini penghubung ke log server |

Penentuan `severity`-nya:

- `auth` — kode `AUTH_FORBIDDEN`.
- `system` — kode `EXCEPTION`, ditambah **9113** yang bentuknya empat digit tapi
  sebenarnya kegagalan infrastruktur.
- `business` — sisanya, termasuk semua kode 90xx/91xx lain dan `UNHANDLED`.

Dokumennya di-dedup per **bucket 10 detik** untuk kombinasi member + gate + kode,
supaya satu burst retry perangkat gate tidak membanjiri layar member dengan error
yang sama berkali-kali.

**3. Log internal** — tabel `BOOMGATES` (respons), `GATE_PARAM` (payload yang
didekripsi), `BOOMGATES_ERROR` (exception), serta tabel log gate per jalur
(`club_house_gate_logs`, `facility_gate_logs`).

:::note[QR statis]

Ada daftar QR statis (dikonfigurasi lewat environment) yang selalu dibalas sukses
tanpa memeriksa header `Authorization` maupun booking. Scan-nya tetap dicatat,
tapi tidak pernah menghasilkan kode error. Kalau sebuah QR selalu lolos di gate
mana pun tanpa punya booking, kemungkinan besar itu QR statis — bukan bug.

:::

## Ringkasan: siapa yang menindaklanjuti

| Ditindaklanjuti oleh | Kode |
| --- | --- |
| **Member sendiri** — salah hari/jam, izin habis | 9005, 9009, 9028, 9109, 9114, 9115, dan penolakan jendela waktu |
| **Petugas / pengelola gate** — pemetaan & konfigurasi gate | 9007, 9007F, 9022, 9107, "Gate tidak aktif" |
| **Integrator perangkat gate** — body request salah | 9012, 9012F, 9100, Unauthorized, Token mismatched |
| **Engineering** — data tidak konsisten atau gangguan sistem | 9003, 9008, 9010, 9011, 9013, 9113, dan semua `500` |
| **Tidak perlu ditindaklanjuti** — sesuai desain | 9014, 9116, 9117, "Anda sudah melakukan scan masuk/keluar" |

## Catatan: kode 9001–9006 di endpoint lama

Endpoint gate versi lama (PHP, `HookController::actionScanQrGate`) memakai rentang
kode yang sama dan masih menyisakan beberapa kode yang **tidak diterbitkan lagi**
oleh endpoint yang sekarang:

| Kode | Pesan | Konteks lama |
| --- | --- | --- |
| 9001 | Resident tidak ditemukan. | Jalur QR residensial per cluster |
| 9002 | qr tidak dikenal. | Jalur appointment visitor / resident service / vaksinasi |
| 9004 | Bukan Resident Kawasan/Cluster ini. | Validasi cluster QR member |
| 9006 | Resident tidak ditemukan. | Varian 9001 di cabang lain |

Kalau salah satu dari empat kode ini muncul di lapangan, artinya perangkat gate
masih menembak endpoint lama — dan itu sendiri yang perlu diperbaiki, bukan
kodenya.
