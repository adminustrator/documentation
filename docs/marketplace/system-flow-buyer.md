---
sidebar_position: 5
description: System Flow atau alur sistem pada buyer marketplace
---

# System Flow - Buyer

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Keterangan kolom

| Tipe           | Deskripsi                                                              |
| -------------- | ---------------------------------------------------------------------- |
| generated text | teks hasil olahan otomatis dari sebuah fungsi atau nilai default kolom |
| session text   | teks yang diambil dari session tertentu                                |
| free text      | teks yang diambil dari input user                                      |
| data text      | teks yang diambil dari data yang tampil                                |

## Add to Cart

Yang terjadi saat user mengklik tombol `+ ke keranjang`.

### Data Flow

Alur datanya adalah sebagai berikut:

1. 'marketplace_carts` untuk menyimpan barang yang dimasukkan ke dalam cart atau keranjang belanja.

   Kolom yang terisi adalah:

   - id: generated text
   - member_uuid: session text
   - marketplace_product_id: data text
   - marketplace_product_variant_id: data text
   - qty: free text
   - note: free text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

## Remove Item from Cart

Yang terjadi saat buyer menghapus item dari cart.

### Data Flow

1. `marketplace_carts` untuk menghapus barang dari cart atau keranjang belanja.

   Kolom yang terisi adalah:

   - deleted_by: session text
   - deleted_at: generated text

   Mengapa tetap disimpan (_soft delete_) dan tidak langsung dihapus (_hard delete_)? Karena data ini bisa dimanfaatkan untuk melihat seberapa besar hubungan antara buyer dan produk terkait.

## Checkout

Yang terjadi pada saat user melakukan checkout dari halaman cart.

### Data Flow

1. `order` untuk menyimpan data order general.

   Kolom yang terisi adalah:

   - id: generated text
   - order_type: generated text : **MARKETPLACE**
   - order_price: data text : harga akhir yang akan dibayar oleh buyer (contoh: 15.000)
   - order_booking_date: generated text : timestamp
   - order_status: generated text : 0 / request invoice
   - member_id: session text
   - order_name: data text : KOTA||NAMA TOKO (contoh: BSD||Toko Baju Rohman)
   - order_member_name: session text : nama buyer
   - order_quantity: data text : total item barang
   - order_note: free text : catatan order
   - order_request: generated text : payload ke payment gateway
   - mp_id: free text : metode pembayaran yang dipilih
   - billing*code: generated text : \*\*(slug-store)-(YmdHis-4)*(random_chars)\*\*
   - order_invoice: generated text : **(MARKETPLACE)/(unix_timestamp)/(first_8_chars_of_store_id)**
   - order*discount: data text : total harga diskon (contoh: 0), \_jika ada voucher*
   - order*voucher_code: data text : kode voucher, \_jika ada voucher*
   - order_base_price: data text : harga sebelum diskon (contoh: 10.000)
   - admin_fee: data text : onesmile fee (contoh: 5.000)

1. `marketplace_orders` untuk menyimpan data order khusus.

   Kolom yang terisi adalah:

   - id: generated text
   - billing_code: generated text : sama dengan di tabel `order`
   - member_uuid: session text
   - marketplace_store_id: data text
   - marketplace_address_id: free text
   - address: generated text : string dari data `marketplace_address_id`
   - total: generated text : sum dari `marketplace_order_items.subtotal`
   - delivery_fee: data text
   - discount: generated text : _jika ada voucher_
   - pre_tax_price: generated text : total - discount
   - onesmile_percentage_fee: data text
   - onesmile_fee: generated text : pre_tax_price \* (onesmile_percentage_fee / 100)
   - tax_percentage: data text
   - tax_amount: generated text : onesmile_fee \* (tax_percentage / 100)
   - grand_total: generated text : pre_tax_price + tax_amount
   - status: generated text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_order_items` untuk menyimpan data produk pada order khusus.

   Kolom yang terisi adalah:

   - id: generated text
   - billing_code: generated text : sama dengan di tabel `order`
   - marketplace_order_id: data text : marketplace_orders.id
   - marketplace_product_version_id: data text
   - price: data text
   - discount: data text
   - qty: free text
   - subtotal: generated text
   - cart_note: free text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_carts`

   Menghapus data (hard delete) cart yang terpilih saat melakukan checkout.

1. `marketplace_transactions`

   Kolom yang terisi adalah:

   - id: generated text
   - marketplace_store_id: data text
   - type: data text
   - amount: data text
   - marketplace_order_id: data text
   - description: generated text
   - status: generated text : (pending)
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

## Webhook saat Invoicing

Yang terjadi setelah berhasil checkout dan menerima webhook dari Payment Gateway tentang detail pemesanan.

### Data Flow

1. `order`, untuk menyimpan response dari Payment Gateway

   Kolom yang terupdate adalah:

   - order_status: generated text : 1 / unpaid
   - order_virtual_number: generated text : jika metode VA
   - order_response: generated text : text response dari Payment Gateway
   - order_redirect_url: generated text : jika metode e-Wallet
   - order_expired_date: generated text : jika tidak payment setelah timestamp ini, maka dianggap kadaluarsa
   - payment_gateway_trx_id: generated text : ID transaksi dari Payment Gateway

## Webhook saat Confirmed Payment

Yang tejadi setelah buyer telah membayar dan sistem menerima webhook dari Payment Gateway tentang detail pembayaran.

### Data Flow

1. `marketplace_order_statuses`

   Kolom yang terisi adalah:

   - id: generated text
   - marketplace_order_id: data text
   - status: generated text : Menunggu Konfirmasi
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `order` untuk menyimpan data pembayaran

   Kolom yang terupdate adalah:

   - order_payment_date: generated text
   - order_status: generated text : 2 / paid
   - order_response: generated text : text response dari Payment Gateway

1. `marketplace_stores`

   Kolom yang terupdate adalah:

   - sold_qty: generated text : total barang yang terjual (increment)
   - sale_amount: generated text : total harga (increment)
   - saldo_amount: generated text

1. `marketplace_products`

   Kolom yang terupdate adalah:

   - sold_qty: generated text : total barang yang terjual (increment)

1. `marketplace_product_variants`

   Kolom yang terupdate adalah:

   - sold_qty: generated text : total barang yang terjual (increment)

1. `marketplace_transactions`

   Kolom yang terupdate adalah:

   - status: generated text : (completed)

## Review dan Rating

Yang terjadi setelah buyer menginput review dan rating pada belanjaannya.

### Data Flow

1. `marketplace_reviews`

   Kolom yang terisi adalah:

   - id: generated text
   - member_uuid: session text
   - name: data text
   - thumbnail: generated text : random user profile picture
   - rating: free text
   - review: free text
   - marketplace_store_id: data text
   - status: generated text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_stores`

   Kolom yang terupdate adalah:

   - rating_total: generated text : total rating yang diberikan (increment++)
   - rating: generated text : rata-rata rating berdasarkan kolom marketplace_reviews.marketplace_store_id

1. `marketplace_products`

   Jika ratingnya pada level produk, maka ini terjadi pula.

   Kolom yang terupdate adalah:

   - rating_total: generated text : total rating yang diberikan (increment++)
   - rating: generated text : rata-rata rating berdasarkan kolom marketplace_reviews.marketplace_store_id

1. `marketplace_product_variants`

   Jika ratingnya pada level produk, maka ini terjadi pula.

   Kolom yang terupdate adalah:

   - rating_total: generated text : total rating yang diberikan (increment++)
   - rating: generated text : rata-rata rating berdasarkan kolom marketplace_reviews.marketplace_store_id
