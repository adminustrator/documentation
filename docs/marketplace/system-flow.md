---
sidebar_position: 4
description: System Flow atau alur sistem pada marketplace
---

# System Flow

## Keterangan kolom

| Tipe           | Deskripsi                                                              |
| -------------- | ---------------------------------------------------------------------- |
| generated text | teks hasil olahan otomatis dari sebuah fungsi atau nilai default kolom |
| session text   | teks yang diambil dari session tertentu                                |
| free text      | teks yang diambil dari input user                                      |

## Registrasi Seller sebagai Partner (B2B)

Proses ini dilakukan oleh Admin OneSmile melalui OSA Panel.

### Data Flow

Alur datanya adalah sebagai berikut:

1. `marketplace_users` untuk menyimpan staf pertama

   Kolom yang terisi adalah:

   - id: generated text
   - name: free text
   - email: free text
   - username: free text
   - password: free text
   - remember_token: generated text
   - organization: related text
   - status: generated text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_stores` untuk menyimpan data utama toko

   Kolom yang terisi adalah:

   - id: generated text
   - city_id: free text
   - city_name: generated text
   - name: free text
   - slug: generated text
   - code: generated text
   - image: free text
   - thumbnail: free text
   - description: free text
   - address: free text
   - marketplace_bank_id: free text
   - bank_account_number: free text
   - store_type: free text
   - minimal_product: generated text
   - minimal_price: generated text
   - tnc_date: generated text
   - percentage: free text
   - sold_qty: generated text
   - sale_amount: generated text
   - withdraw_amount: generated text
   - saldo_amount: generated text
   - rating: generated text
   - rating_total: generated text
   - status: generated text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_store_users` untuk mengasosiasikan staf terhadap toko

   Kolom yang terisi adalah:

   - id: generated text
   - marketplace_store_id: generated text
   - marketplace_user_id: generated text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_store_hours` untuk menyimpan jam operasional toko

   Kolom yang terisi adalah:

   - id: generated text
   - marketplace_store_id: generated text
   - day_of_week: free text
   - open_time: free text
   - close_time: free text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text

1. `marketplace_socials` untuk menyimpan data media sosial toko

   Kolom yang terisi adalah:

   - id: generated text
   - marketplace_store_id: generated text
   - platform: free text
   - username: free text
   - status: generated text
   - created_by: session text
   - updated_by: session text
   - created_at: generated text
   - updated_at: generated text
