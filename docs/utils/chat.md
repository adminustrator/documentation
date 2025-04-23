---
sidebar_position: 3
description: OneSmile Chat API
---

# Chat

:::tip[Penyusun]

- Muhlis Habib Pradana :: Backend & Website Lead - Operational Technology

:::

## Getting Started

### General Requirement

- As OneSmile APIs are RESTful, partners should send HTTPS requests to URL addresses to call a command.
- OneSmile APIs have two endpoints (common address prefixes for command URLs), one for sandbox calls and one for production calls.
- You also use different tokens for authenticating staging and production requests, which we’ll cover in the Authentication section.
- **Staging:** [**https://az-api-dev.onesmile.digital/api-v2**](https://az-api-dev.onesmile.digital/api-v2)
- **Production:** [**https://api.onesmile.digital/api-v2**](https://api.onesmile.digital/api-v2)

All OneSmile API requests must use [**HTTPS**](http://en.wikipedia.org/wiki/HTTP_Secure). Any HTTP calls will fail with a dropped connection.

### API Endpoints

OneSmile has four APIs.

With OneSmile APIs you can perform the following actions:

| API                 | Details            | Format                     | Endpoint Provided by OneSmile | Endpoint Provided by Partner | API Defined by |
| ------------------- | ------------------ | -------------------------- | ----------------------------- | ---------------------------- | -------------- |
| Create Room Message | Create New Message | POST /generalmessagecreate | ✅                            |                              | OneSmile       |
| Get Message List    | Get List Message   | POST /generalmessagelist   | ✅                            |                              | OneSmile       |
| Get Message Detail  | Get List Chat      | POST /generalmessagedetail | ✅                            |                              | OneSmile       |
| Send Message        | Send Chat          | POST /generalmessagesent   | ✅                            |                              | OneSmile       |

## Authentication Method

OneSmile API requests must have authentication or they fail with message Invalid Parameters response.

### Request Headers

Every request to GrabExpress API has to have the following request headers

| Header Parameter | Required | Description        |
| ---------------- | -------- | ------------------ |
| `Content-Type`   | yes      | `application/json` |
| `X-Auth-Token`   | yes      | Auth Token         |

### Create Room Message

**Endpoint URL:** `POST /generalmessagecreate`

#### Request

```json
curl --request POST \
  --url https://az-api-dev.onesmile.digital/api-v2/generalmessagecreate \
  --header 'Content-Type: application/json' \
  --header 'X-Auth-Token: <TOKEN_HERE>' \
  --data '{
    "recipient_id": "5bfa4fd0-1dd3-4055-86f4-b2f56ec2b5d",
    "image":"",
    "document":"",
    "message":""
}'
```

> For requests with Token , make sure to replace `<TOKEN_HERE>` with your OAuth token.

> The above command returns response code `200 OK`:

#### Request Header Parameters

| Parameter    | Type   | Description                                                                                                                                     | Required |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. | ✅       |
| X-Auth-Token | String | Header required for authorization purpose.                                                                                                      | ✅       |

#### Request Body Parameters

| Parameter      | Type   | Description                    | Required |
| -------------- | ------ | ------------------------------ | -------- |
| `recipient_id` | String | Recipient ID                   | ✅       |
| `role`         | String | Type of role i.e `1`, `2`, `3` | ❌       |
| `image`        | String | Image                          | ❌       |
| `document`     | String | Document                       | ❌       |
| `message`      | String | Message                        | ❌       |

#### Response

```json
{
  "success": false,
  "msg": "",
  "response": {
    "room_id": "",
    "created_date": "",
    "message": "",
    "fullname": "",
    "member_photo": ""
  }
}
```

#### Response Header Parameters

| Parameter    | Type   | Description                                                                                                                                     |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. |

#### Response Body Parameters

| Parameter             | Type    | Description                            |
| --------------------- | ------- | -------------------------------------- |
| success               | boolean | Status keberhasilan dari response      |
| msg                   | string  | Pesan terkait hasil response           |
| response              | object  | Objek data hasil response              |
| response.room_id      | string  | ID dari ruang chat (kosong jika gagal) |
| response.created_date | string  | Tanggal pembuatan room (jika ada)      |
| response.message      | string  | Pesan terakhir (jika ada)              |
| response.fullname     | string  | Nama lengkap teman                     |
| response.member_photo | string  | URL foto profil teman                  |

### Get Message List

**Endpoint URL:** `POST /generalmessagelist`

#### Request

```json
curl --request POST \
  --url https://api.onesmile.digital/api-v2/generalmessagelist \
  --header 'X-Auth-Token: <TOKEN_HERE>'
```

> For requests with Token , make sure to replace `<TOKEN_HERE>` with your OAuth token.

> The above command returns response code `200 OK`:

#### Request Header Parameters

| Parameter    | Type   | Description                                                                                                                                     | Required |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. | ✅       |
| X-Auth-Token | String | Header required for authorization purpose.                                                                                                      | ✅       |

#### Response

```json
{
  "success": true,
  "msg": "OK",
  "response": [
    {
      "room_id": 3381,
      "created_date": "2024-12-30 10:40:34.705587",
      "message": "tes",
      "fullname": "nia fikayanti",
      "member_photo": "https://az-api-dev.onesmile.digital/img/photo/avatar.png",
      "recipient_id": "3448",
      "tgl": "30 Des 2024",
      "un_seen": 1
    }
  ]
}
```

#### Response Header Parameters

| Parameter    | Type   | Description                                                                                                                                     |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. |

#### Response Body Parameters

| Parameter               | Type    | Description                                     |
| ----------------------- | ------- | ----------------------------------------------- |
| success                 | boolean | Status keberhasilan dari response               |
| msg                     | string  | Pesan status dari response                      |
| response[].room_id      | number  | ID unik dari room                               |
| response[].created_date | string  | Tanggal dan waktu pembuatan pesan terakhir      |
| response[].message      | string  | Isi pesan terakhir (bisa null)                  |
| response[].fullname     | string  | Nama lengkap anggota atau penerima pesan        |
| response[].member_photo | string  | URL foto profil anggota                         |
| response[].recipient_id | string  | ID penerima (bisa dalam format khusus)          |
| response[].tgl          | string  | Tanggal dalam format lokal (misal: 30 Des 2024) |
| response[].un_seen      | number  | Jumlah pesan yang belum dibaca                  |

### Get Message Detail

**Endpoint URL:** `POST /generalmessagedetail`

#### Request

```json
curl --request POST \
  --url https://api.onesmile.digital/api-v2/generalmessagedetail \
  --header 'Content-Type: application/json' \
  --header 'X-Auth-Token: <TOKEN_HERE>' \
  --data '{
  "sf": 0,
  "limit": 10,
  "room_id": ""
}'
```

> For requests with Token , make sure to replace `<TOKEN_HERE>` with your OAuth token.

> The above command returns response code `200 OK`:

#### Request Header Parameters

| Parameter    | Type   | Description                                                                                                                                     | Required |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. | ✅       |
| X-Auth-Token | String | Header required for authorization purpose.                                                                                                      | ✅       |

#### Request Body Parameters

| Parameter | Type   | Description                          | Required |
| --------- | ------ | ------------------------------------ | -------- |
| room_id   | string | ID dari room chat                    | ✅       |
| sf        | number | Start from (offset untuk pagination) | ❌       |
| limit     | number | Jumlah data yang ingin diambil       | ❌       |

#### Response

```json
{
  "success": true,
  "msg": "OK",
  "room_detail": {
    "id": 585,
    "room_id": 123,
    "member_id": "29",
    "created_date": "2021-09-03 13:32:02.600787",
    "message": null,
    "status": 2,
    "recipient_id": "RESIDENT_BILLING|666",
    "image": null,
    "document": null,
    "produk_id": 0,
    "role": 0,
    "message_uuid": "27a196f7-0197-4373-a1fa-2d0849ce09f4",
    "message_room_uuid": "357a625c-0b4f-4d57-b5e6-da0507557787",
    "fullname": "",
    "room_name": "",
    "member_photo": "https://api.onesmile.digital/img/photo/avatar.png"
  }
}
```

#### Response Header Parameters

| Parameter    | Type   | Description                                                                                                                                     |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. |

#### Response Body Parameters

| Parameter                     | Type    | Description                             |
| ----------------------------- | ------- | --------------------------------------- |
| success                       | boolean | Status keberhasilan dari response       |
| msg                           | string  | Pesan status dari response              |
| room_detail.id                | number  | ID unik dari detail room                |
| room_detail.room_id           | number  | ID dari room                            |
| room_detail.member_id         | string  | ID dari anggota                         |
| room_detail.created_date      | string  | Tanggal dan waktu pembuatan detail room |
| room_detail.message           | string  | Pesan terakhir (bisa null)              |
| room_detail.status            | number  | Status room                             |
| room_detail.recipient_id      | string  | ID penerima dalam format khusus         |
| room_detail.image             | string  | URL gambar yang dikirim (bisa null)     |
| room_detail.document          | string  | URL dokumen yang dikirim (bisa null)    |
| room_detail.produk_id         | number  | ID produk terkait (jika ada)            |
| room_detail.role              | number  | Peran member dalam room                 |
| room_detail.message_uuid      | string  | UUID untuk pesan                        |
| room_detail.message_room_uuid | string  | UUID untuk pesan dalam konteks room     |
| room_detail.fullname          | string  | Nama lengkap anggota                    |
| room_detail.room_name         | string  | Nama room                               |
| room_detail.member_photo      | string  | URL foto profil anggota                 |

### Send Message

**Endpoint URL:** `POST /generalmessagesent`

#### Request

```json
curl --request POST \
  --url https://api.onesmile.digital/api-v2/generalmessagesent \
  --header 'Content-Type: application/json' \
  --header 'X-Auth-Token: <TOKEN_HERE>' \
  --data '{
    "recipient_id": "5bfa4fd0-1dd3-4055-86f4-b2f56ec2b5d",
    "room_id" : "",
    "image":"",
    "document":"",
    "message":""
}'
```

> For requests with Token , make sure to replace `<TOKEN_HERE>` with your OAuth token.

> The above command returns response code `200 OK`:

#### Request Header Parameters

| Parameter    | Type   | Description                                                                                                                                     | Required |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. | ✅       |
| X-Auth-Token | String | Header required for authorization purpose.                                                                                                      | ✅       |

#### Request Body Parameters

| Parameter      | Type   | Description                    | Required |
| -------------- | ------ | ------------------------------ | :------: |
| `recipient_id` | String | Recipient ID                   |    ✅    |
| `role`         | String | Type of role i.e `1`, `2`, `3` |    ❌    |
| `image`        | String | Image                          |    ❌    |
| `document`     | String | Document                       |    ❌    |
| `message`      | String | Message                        |    ❌    |

#### Response

```json
{
  "success": true,
  "msg": "OK",
  "response": {
    "id": 585,
    "room_id": 123,
    "member_id": "29",
    "created_date": "2021-09-03 13:32:02.600787",
    "message": null,
    "status": 2,
    "recipient_id": "RESIDENT_BILLING|666",
    "image": null,
    "document": null,
    "produk_id": 0,
    "role": 0,
    "message_uuid": "27a196f7-0197-4373-a1fa-2d0849ce09f4",
    "message_room_uuid": "357a625c-0b4f-4d57-b5e6-da0507557787",
    "fullname": "",
    "room_name": "",
    "member_photo": "https://api.onesmile.digital/img/photo/avatar.png"
  }
}
```

#### Response Header Parameters

| Parameter    | Type   | Description                                                                                                                                     |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Content-Type | String | The content type of the request body. You must use `application/json` for this header because OneSmile doesn't support other formats currently. |

#### Response Body Parameters

| Parameter                  | Type    | Description                             |
| -------------------------- | ------- | --------------------------------------- |
| success                    | boolean | Status keberhasilan dari response       |
| msg                        | string  | Pesan status dari response              |
| response.id                | number  | ID unik dari detail room                |
| response.room_id           | number  | ID dari room                            |
| response.member_id         | string  | ID dari anggota                         |
| response.created_date      | string  | Tanggal dan waktu pembuatan detail room |
| response.message           | string  | Pesan terakhir (bisa null)              |
| response.status            | number  | Status room                             |
| response.recipient_id      | string  | ID penerima dalam format khusus         |
| response.image             | string  | URL gambar yang dikirim (bisa null)     |
| response.document          | string  | URL dokumen yang dikirim (bisa null)    |
| response.produk_id         | number  | ID produk terkait (jika ada)            |
| response.role              | number  | Peran member dalam room                 |
| response.message_uuid      | string  | UUID untuk pesan                        |
| response.message_room_uuid | string  | UUID untuk pesan dalam konteks room     |
| response.fullname          | string  | Nama lengkap anggota                    |
| response.room_name         | string  | Nama room                               |
| response.member_photo      | string  | URL foto profil anggota                 |
