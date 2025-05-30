---
sidebar_position: 2
description: the details about integer columns
---

# Integer Columns

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## appointment_member

### status

| Value | Description                                         |
| ----- | --------------------------------------------------- |
| 0     | DRAFT / OPEN                                        |
| 1     | ACTIVE / IN PROCESS                                 |
| 2     | FINISHED                                            |
| 3     | DATE CONFIRMED / RESCHEDULED / FINISHED / CLARIFIED |
| 4     | CANCELLED                                           |
| 5     | COMPLETED                                           |
| 6     | DATE CONFIRMED / GATE IN                            |
| 7     | GATE OUT                                            |
| 8     | SCAN BY OFFICER / HANDLE BY CS                      |
| 9     | DONE BY CS                                          |
| 10    | FINISHED BY SYSTEM                                  |
| 11    | CLARIFIED                                           |
| 13    | RESCHEDULED                                         |
| 14    | CONFIRMED                                           |
| 15    | CANCEL BY CONFIRMATION                              |
| 16    | CANCEL BY ATTENDANCE                                |
| 17    | CANCEL BY SYSTEM                                    |
| 18    | FINISHED BY ATTENDANCE                              |
| 19    | CONFIRMATION BY ATTENDANCE                          |

## banner_announcement

### status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

## banner_digital_hub_livestream

### bdh_status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

## city_guide_story

### status

| Value | Description  |
| ----- | ------------ |
| 0     | UNPUBLISHED  |
| 1     | PUBLISHED    |
| 2     | NEED CONFIRM |
| 3     | REJECTED     |

## club_house_member

### status

| Value | Description                    |
| ----- | ------------------------------ |
| 0     | DRAFT                          |
| 1     | ACTIVE                         |
| 2     | FINISHED                       |
| 3     | FINISHED                       |
| 4     | CANCELLED                      |
| 6     | GATE IN                        |
| 7     | GATE OUT                       |
| 8     | HANDLE BY CS                   |
| 9     | DONE BY CS                     |
| 10    | FINISHED BY SYSTEM             |
| 11    | CLARIFIED                      |
| 13    | RESCHEDULED                    |
| 14    | CONFIRMED                      |
| 15    | CANCEL BY CONFIRMATION         |
| 16    | CANCEL BY ATTENDANCE           |
| 17    | CANCEL BY SYSTEM / NOT PRESENT |
| 18    | FINISHED BY ATTENDANCE         |
| 19    | CONFIRMATION BY ATTENDANCE     |

### membership_type

| Value | Description |
| ----- | ----------- |
| 1     | MASTER      |
| 3     | USER        |

### membership_status

| Value | Description    |
| ----- | -------------- |
| 0     | TIDAK TERBAYAR |
| 1     | TERBAYAR       |

## complaint_member_item

### status

| Value | Description |
| ----- | ----------- |
| 0     | OPEN        |
| 1     | IN PROCESS  |
| 2     | FINISHED    |
| 3     | CLARIFIED   |
| 4     | CANCELLED   |
| 5     | COMPLETED   |

## event_ticket_order

### status

| Value | Description         |
| ----- | ------------------- |
| 1     | Pembayaran Selesai  |
| 2     | Menunggu Pembayaran |
| 3     | Selesai             |
| 4     | Event telah berkhir |
| 5     | ???                 |

## facility_member

### status

| Value | Description                         |
| ----- | ----------------------------------- |
| 0     | Draft                               |
| 1     | Dipesan / Open                      |
| 2     | Completed / Close                   |
| 3     | Waiting for Confirmation / Finished |
| 4     | Canceled / Rejected                 |
| 6     | Revised                             |
| 7     | Canceled By User / More Info Needed |
| 8     | Cancel Approved / In Process        |
| 9     | In Proccess / Completed             |
| 10    | Telah Lewat                         |
| 13    | Telah Lewat                         |

## facility_sub_services

### status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

## facility_coach

### status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

## facility_group

### status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

## general_message_room

### type

| Value | Description   |
| ----- | ------------- |
| 1     | Personal      |
| 2     | Marketplace   |
| 5     | CS            |
| 6     | Not used      |
| 7     | Washteria (?) |

### status

| Value | Description |
| ----- | ----------- |

## general_message

### status

| Value | Description |
| ----- | ----------- |
| 1     | Unread      |
| 2     | Read        |

### role

| Value | Description |
| ----- | ----------- |

## kategori

### kategori_status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |

### kategori_jns

| Value | Description |
| ----- | ----------- |
| 0     | PUBLIC      |
| 1     | CLOSED      |

## member

### member_status

| Value | Description |
| ----- | ----------- |
| 0     | DISABLED    |
| 1     | ENABLED     |

### member_network

| Value | Description   |
| ----- | ------------- |
| 1     | IPL           |
| 1.1   | KODE KELUARGA |

### member_left

| Value | Description |
| ----- | ----------- |
| 0     | ONESMILE    |
| 1     | SML         |

### member_right

| Value | Description |
| ----- | ----------- |
| 0     | USER        |
| 1     | TESTER      |

### member_type

| Value | Description |
| ----- | ----------- |
| 1     | PARENT      |
| 2     | CHILD       |

### member_gender

| Value | Description |
| ----- | ----------- |
| 1     | PRIA        |
| 2     | WANITA      |

### member_primary_account

| Value | Description |
| ----- | ----------- |
| 0     | NO          |
| 1     | YES         |

### member_cro

| Value | Description  | Note                                                                 |
| ----- | ------------ | -------------------------------------------------------------------- |
| 0     | Resident     | `AND member_facility.member_id::numeric = facility_member.member_id` |
| 0     | Non Resident |
| 1     | Resident     |
| 2     | Merchant     |

## member_contractor

### status

| Value | Description |
| ----- | ----------- |
| 0     | DELETED     |
| 1     | ACTIVE      |

## member_facility

### status

| Value | Description       |
| ----- | ----------------- |
| 0     | NEED CONFIRMATION |
| 1     | APPROVED          |
| 2     | REJECTED          |

### member_type

| Value | Description |
| ----- | ----------- |
| 1     | Individu    |
| 2     | Familiy     |
| 3     | Couple      |

## member_notification_setting

### mn_type

| Value | Description      |
| ----- | ---------------- |
| 1     | ALL NOTIFICATION |
| 2     | EMAIL            |
| 3     | PROMOTION        |
| 4     | UPDATE           |
| 5     | EVENT            |
| 6     | TRANSACTION      |
| 7     | CHAT             |
| 8     | DISCUSSION       |

### status

| Value | Description |
| ----- | ----------- |
| 0     | ???         |
| 1     | ???         |

## member_pengiriman

### mp_status

| Value | Description |
| ----- | ----------- |
| 0     | ???         |
| 1     | ???         |

## member_qrcode

### mr_status

| Value | Description |
| ----- | ----------- |
| 0     | ???         |
| 1     | ???         |

## member_reff

### mr_status

| Value | Description |
| ----- | ----------- |
| 0     | ???         |
| 1     | ???         |

## news

### news_status

| Value | Description |
| ----- | ----------- |
| 0     | UNPUBLISHED |
| 1     | PUBLISHED   |
| 2     | HIGHLIGHT   |

## order

### order_status

| Value | Description                            |
| ----- | -------------------------------------- |
| 0     | Request Order                          |
| 1     | Menunggu Pembayaran                    |
| 2     | Terbayar                               |
| 3     | Waiting By System                      |
| 4     | Pending By System Waiting Notification |
| 5     | Success                                |
| 6     | Canceled                               |
| 7     | Canceled By System                     |
| 8     | Expired                                |

## sml_events

### status

| Value | Description |
| ----- | ----------- |
| 0     | Inactive    |
| 1     | Active      |

## sml_event_bookings

### status

| Value | Description      |
| ----- | ---------------- |
| 0     | Berhasil Dipesan |
| 1     | Sudah Dibayar    |

## sml_event_packages

### status

| Value | Description   |
| ----- | ------------- |
| 0     | Dijadwalkan   |
| 1     | Sudah Diambil |

## survey_member

### status

| Value | Description      |
| ----- | ---------------- |
| 0     | Draft            |
| 1     | Open             |
| 2     | Close            |
| 3     | Finished         |
| 4     | Rejected         |
| 6     | Revised          |
| 7     | More Info Needed |
| 8     | In Process       |
| 9     | Completed        |

## voucher

### amount_type

| Value      | Description   |
| ---------- | ------------- |
| FIXED      | Fixed Amount  |
| PERCENTAGE | Percentage    |
| _else_     | NOT SPECIFIED |

### type

| Value              | Description                              |
| ------------------ | ---------------------------------------- |
| ONLINE_SINGLE      | Online Voucher Auto Generate Single Code |
| ONLINE_UNIQUE_CODE | Online Voucher Prepared Unique Code      |
| UNIQUE             | Offline Unique Code Voucher              |
| IMAGE              | Online Unique Image Voucher              |
| BARCODE            | Offline Unique Barcode Voucher           |
| _else_             | NOT SPECIFIED                            |

### status

| Value  | Description      |
| ------ | ---------------- |
| 0      | UNPUBLISHED      |
| 1      | USER VOUCHER     |
| 2      | PUBLIC VOUCHER   |
| 3      | REDEEMED VOUCHER |
| 4      | MASTER VOUCHER   |
| _else_ | NOT SPECIFIED    |

### voucher_jenis_member

| Value  | Description   |
| ------ | ------------- |
| 0      | All           |
| 1      | Resident      |
| 2      | Merchant      |
| _else_ | NOT SPECIFIED |
