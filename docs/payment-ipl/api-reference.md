---
sidebar_position: 2
description: Referensi endpoint HTTP servis pembayaran IPL & Air.
---

# Referensi API

:::tip[Penyusun]

- Bazrira Noerfirdiansyah :: Fullstack Developer Senior Associate - Operational Technology

:::

Semua endpoint berada di bawah base path **`/api/v2`**. Host per environment lihat
[Base URLs](../base-url.md).

## Konvensi

### Grup & autentikasi

| Grup | Header autentikasi | Keterangan |
| ---- | ------------------ | ---------- |
| **IPL** | `X-Auth-Token: Bearer <token>` | Token member; ditolak `401` bila tidak dikenal. |
| **Internal** | `api-key: <jwt>` | Dipakai oleh sistem RealBit / back-office. |
| **Kwitansi PDF** (per-invoice) | _tidak ada_ | Endpoint publik yang menghasilkan PDF. |
| **Riwayat PDF** | Signed URL (`?token=`) | Token JWT berbatas waktu, dibuat oleh `bill/history`. |

### Response envelope

Endpoint JSON mengembalikan bentuk:

```json
{
  "status": "OK",
  "code": 200,
  "data": {},
  "message": null,
  "error": null
}
```

`data`/`message`/`error` bersifat `omitempty`. Endpoint berpaginasi menambahkan `pagination`
(lihat `GET /ipl/bill/history`). Endpoint PDF/CSV mengembalikan _file stream_, bukan envelope
JSON.

:::note
Sumber kontrak API: anotasi Swagger pada `app/ipl/registry.go` & `app/internal/registry.go`,
DTO di `app/ipl/usecase/`, serta `docs/swagger.yaml`. Path resmi mengikuti routing di
`app/server.go` (beberapa contoh path pada `swagger.yaml` sedikit berbeda — lihat catatan per
endpoint).
:::

---

## Endpoint IPL

Seluruh endpoint di bawah ini memerlukan header `X-Auth-Token`.

### `GET /ipl`

Daftar unit/IPL milik member yang terkait dengan token.

**Respons `200` — `data`: array `MemberIPLResponse`:**

| Field | Tipe | Keterangan |
| ----- | ---- | ---------- |
| `member_id` | int | ID member. |
| `member_name` | string | Nama member. |
| `member_status` | int | Status member. |
| `ipl` | string | Nomor IPL. |
| `address` | string | Alamat unit. |
| `kategori_id` | int | ID kategori (menentukan `realbit_code`). |
| `type` | string | Tipe unit. |
| `selected` | bool | Penanda unit terpilih. |

### `GET /ipl/statistic`

Statistik nominal tagihan yang dibayar (IPL/Air) untuk bulan tertentu.

| Query | Tipe | Wajib | Keterangan |
| ----- | ---- | ----- | ---------- |
| `month` | int | ya | Bulan (`1`–`12`). |

**Respons `200` — `data`: array `StatisticResponse`:** `monthAndYear`, `billTotal`,
`billTotalWithCurrency`, `cubicStart`, `cubicEnd`, `cubicUsage`.

### `GET /ipl/bill/summary`

Ringkasan tagihan berjalan member, diambil dari RealBit.

**Respons `200` — `data`: `BillSummaryResponse`** (objek bersarang), memuat antara lain:

| Field | Tipe | Keterangan |
| ----- | ---- | ---------- |
| `customerName`, `address` | string | Data pelanggan. |
| `dueDate`, `period` | string | Jatuh tempo & periode. |
| `total`, `totalWithCurrency`, `arrears` | string | Total & tunggakan. |
| `isQrisPayment` | bool | Menandai kelayakan pembayaran QRIS. |
| `ipl` | `BillSummaryIPL` | Rincian IPL (`iplNumber`, `total`, `iplPenalty`, ...). |
| `water` | `BillSummaryWater` | Rincian Air (`waterNumber`, `cubication`, `total`, ...). |
| `penalty`, `adminFee`, `administrasion`, `materai` | objek | Komponen biaya. |
| `outstanding` | `Outstanding` | Total + `detail[]` per periode. |
| `outstandingHistory` | array `OutstandingHistory` | `period`, `total`, `totalWithCurrency`. |
| `payment` | `PaymentRequestResponse` | Info pembayaran terakhir (bila ada). |

### `GET /ipl/bill/history`

Riwayat tagihan (berpaginasi), atau — bila `isPDF=true` — menghasilkan **signed URL** untuk
mengunduh PDF riwayat.

| Query | Tipe | Wajib | Keterangan |
| ----- | ---- | ----- | ---------- |
| `page` | int | tidak | Halaman (paginasi). |
| `status` | string | tidak | Filter status pembayaran. |
| `startYearMonth` | string | tidak | Awal rentang, format `YYYYMM`. |
| `endYearMonth` | string | tidak | Akhir rentang, format `YYYYMM`. |
| `isPDF` | bool | tidak | Bila `true`, mengembalikan signed URL, bukan data. |

**Respons `200` (default, `isPDF` kosong/`false`)** — `data`: array `BillHistoryResponse`,
disertai objek `pagination`:

```json
{
  "status": "OK",
  "code": 200,
  "data": [ { "period": "202403", "total": "150000", "status": "PAID" } ],
  "pagination": {
    "currentPage": 1,
    "pageSize": 10,
    "totalPages": 5,
    "totalItems": 42,
    "hasMore": true
  }
}
```

**Respons `200` (`isPDF=true`)** — `data`: `SignedPDFURLResponse`:

| Field | Tipe | Keterangan |
| ----- | ---- | ---------- |
| `signedUrl` | string | URL unduhan ke `/api/v2/receipt-ipl/bill-history?token=...`. |
| `expiresAt` | int (unix) | Waktu kedaluwarsa token. |
| `expiresIn` | int | Masa berlaku (menit) — saat ini **2 menit**. |
| `pdfType` | string | `bill-history`. |
| `memberIpl` | string | Nomor IPL. |
| `period` | string | Rentang periode (bila `startYearMonth`+`endYearMonth` diisi). |

### `GET /ipl/payment/period`

Daftar periode/tenor pembayaran (mis. cicilan). Endpoint ini _gateway-agnostic_.

**Respons `200` — `data`: array `PaymentPeriodResponse`:** `paymentPeriodId`, `month`,
`freeMonth`, `description`.

### `GET /ipl/banner/special-offer`

Banner penawaran khusus IPL.

**Respons `200` — `data`: `SpecialOfferResponse`:** `bannerIplId`, `bannerIplTitle`,
`bannerIplDescription`, `bannerIplFile`, `bannerIplLink`, `bannerIplDate`, `bannerIplStatus`.

---

## Endpoint Kwitansi & Unduhan PDF

### `GET /receipt-ipl/{iplNumber}/{yearMonth}/{amount}`

Menghasilkan **PDF kwitansi** pembayaran satu invoice (IPL & Air). Endpoint publik (tanpa
middleware auth).

| Path param | Contoh | Keterangan |
| ---------- | ------ | ---------- |
| `iplNumber` | `0036945` | Nomor IPL/pelanggan. |
| `yearMonth` | `202403` | Periode `YYYYMM`. |
| `amount` | `150000` | Nominal (integer). |

**Respons `200`:** `application/pdf`.

:::note
Pada `docs/swagger.yaml` endpoint ini terdaftar dengan dua path param
(`/receipt-ipl/{iplNumber}/{yearMonth}`), namun routing sebenarnya di `app/server.go:87`
menggunakan **tiga** segmen termasuk `{amount}`.
:::

### `GET /receipt-ipl/bill-history?token=...`

Mengunduh **PDF riwayat tagihan** (surat tagihan multi-periode). Diproteksi middleware
**`AuthSignedURL`**; `token` diperoleh dari `GET /ipl/bill/history?isPDF=true`.

| Query | Tipe | Wajib | Keterangan |
| ----- | ---- | ----- | ---------- |
| `token` | string | ya | Signed URL token (JWT berbatas waktu). |

**Respons `200`:** `application/pdf`. **`401`** bila token tidak valid/kedaluwarsa.

---

## Endpoint Internal

Untuk konsumsi sistem (RealBit / back-office), memakai header **`api-key`** (middleware
`AuthDownloadFile`).

### `POST /internal/webhook/ipl-monthly`

Webhook _bulk upsert_ data billing bulanan dari RealBit.

:::note
Path resmi adalah **`/api/v2/internal/webhook/ipl-monthly`** (`app/server.go:99`). Ringkasan
Swagger lama mencantumkan `/internal/ipl-monthly` — gunakan path `webhook/` di atas.
:::

**Body:** array `IplMonthlyCreateBodyRequest`, mis.:

```json
[
  {
    "customer_id": "…",
    "customer_name": "…",
    "unit_id": "…",
    "unit_code": "…",
    "period": "202403",
    "due_date": "2024-03-20",
    "release_date_billing": "2024-03-01",
    "ipl_amount": 100000,
    "ipl_penalty": 0,
    "water_amount": 50000,
    "water_penalty": 0,
    "material_amount": 0,
    "penalty_amount": 0,
    "outstanding_amount": 0,
    "total_billing": 150000,
    "start_usage_water_meter": 100,
    "end_usage_water_meter": 110,
    "total_usage_water_meter": 10,
    "status": "UNPAID",
    "email": "…",
    "phone_number": "…"
  }
]
```

**Respons `201`:** `data` = `"success update or create billing ipl"`.

### `GET /internal/download/ipl-weekly-report`

Mengekspor laporan IPL mingguan sebagai **CSV** (_chunked stream_).

| Query | Tipe | Wajib | Keterangan |
| ----- | ---- | ----- | ---------- |
| `startDate` | string | ya | Tanggal awal. |
| `endDate` | string | ya | Tanggal akhir. |
| `statusPayment` | string | tidak | Filter status pembayaran. |

**Respons `200`:** `text/csv` (`Content-Disposition: attachment`). Bila `startDate` dan
`endDate` kosong, mengembalikan array kosong dalam envelope JSON.

---

## Endpoint infrastruktur

| Method & path | Keterangan |
| ------------- | ---------- |
| `GET /api/v2/` | Pesan sambutan (`Welcome Payment API IPL & Water Service`). |
| `GET /api/v2/ipl/document` | Halaman dokumentasi Swagger (render `views/document.html`). |
| `GET /swagger/*` | Swagger UI. |
| `GET /assets/*` | Berkas statik (mis. logo pada PDF). |

---

## Endpoint yang tidak didokumentasikan

Endpoint inisiasi pembayaran melalui gateway berbasis QRIS (`POST /ipl/payment/request`,
`POST /ipl/payment/status/update`, `GET /ipl/payment/method`, dan callback
`POST /internal/dsp-payment/paymentnotification`) **sengaja tidak didokumentasikan** di sini
karena kanal tersebut berstatus _deprecated_. Endpoint tersebut mungkin masih ada di kode namun
tidak menjadi bagian dari kontrak yang direkomendasikan.
