---
sidebar_position: 7
description: Kolom-kolom status pada bundle Marketplace
---

# Statuses

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## marketplace_stores

Kolom `status`

- **Pending**
- **Publish**
- **Close**
- **Drop**
- **Suspend**

## marketplace_products

Kolom `status`

- **Draft**
- **Publish**

## marketplace_product_variants

Kolom `status`

- **Draft**
- **Publish**

## marketplace_product_images

Kolom `status`

- **Draft**
- **Publish**

## marketplace_orders

Kolom `status` berdasarkan urutan historikal.

- **Unpaid** : Saat buyer selesai checkout, telah mendapatkan QRIS dan belum bayar.
- **Pending** : Saat buyer telah bayar dan menunggu konfirmasi pesanan oleh seller.
- **Processing** : Seller telah mengonfirmasi pesanan buyer dan sedang menyiapkannya.
- **Ready** : (BELUM DIPAKAI) Jika pesanan siap di-pick-up oleh shipper.
- **Shipped** : Seller sedang mengantarkan pesanan ke alamat buyer.
- **Delivered** : Seller telah mengantarkan pesanan ke alamat buyer.
- **Canceled** : Seller menolak pesanan.
- **Returned** : (BELUM DIPAKAI) Saat pesanan dikembalikan ke seller oleh buyer.
- **Refunded** : (BELUM DIPAKAI) Saat pesanan dibatalkan dan uang telah dikembalikan.

## marketplace_order_statuses

Kolom `status` berdasarkan urutan historikal.

- **Menunggu Pembayaran** : Saat buyer selesai checkout, telah mendapatkan QRIS dan belum bayar.
- **Pembayaran Berhasil** : Saat buyer telah bayar pesanannya.
- **Menunggu Konfirmasi** : Saat buyer telah bayar dan menunggu konfirmasi pesanan oleh seller.
- **Pesanan Diproses** : Seller telah mengonfirmasi pesanan buyer dan sedang menyiapkannya.
- **Pesanan Dalam Proses Pengiriman** : Seller sedang mengantarkan pesanan ke alamat buyer.
- **Pesanan Terkirim** : Seller telah mengantarkan pesanan ke alamat buyer.
- **Pesanan Selesai** : Buyer mengonfirmasi pesanannya telah sampai.
- **Pesanan Dibatalkan** : Seller menolak pesanan.
- **Pembayaran Kadaluarsa** : Saat buyer gagal membayar pesanannya dalam waktu kurun tertentu.

## marketplace_deposits

Kolom `status` berdasarkan urutan historikal.

- **Draft** : Proses persiapan deposit sedang berlangsung.
- **Waiting for Approval** : Proses persiapan deposit telah selesai dan menunggu hasil approval.
- **Rejected** : Proses deposit ditolak.
- **Processing** : Proses deposit diterima dan proses deposit ke seller sedang berlangsung.
- **Completed** : Proses deposit ke seller telah selesai.

## marketplace_transactions

Kolom `status`

- **pending**
- **completed**
- **failed**

Kolom `type`

- **income**
- **withdrawal**
- **refund**
- **deposit**