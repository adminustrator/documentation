---
sidebar_position: 1
description: Upload File ke Azure Storage
---

# Upload File

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Request Endpoint

```
POST https://kube-prd.az-api.onesmile.digital/homepage/api/v2/utils/upload
```

## Headers

| Header Parameter | Type   | Description |
| ---------------- | ------ | ----------- |
| X-Auth-Token     | String | Token       |

## Body

| Body Parameter | Type   | Description                   | Required |
| -------------- | ------ | ----------------------------- | :------: |
| bucket         | String | Nama bucket untuk objek       |    ✅    |
| content        | String | Objek gambar/PDF dalam Base64 |    ✅    |
| filename       | String | Nama objek                    |    ❌    |

## Example responses

Status: `200`

```json
{
  "success": true,
  "msg": "Sukses",
  "data": {
    "path": "testing/59-upload_file_72d178fc3800717b.pdf",
    "url": "https://onesmilestoragedev.blob.core.windows.net/onesmile/testing/59-upload_file_72d178fc3800717b.pdf"
  }
}
```

Status: `400`

Error validasi

```json
{
  "status": false,
  "msg": "Bad Request",
  "errors": [
    {
      "type": "field",
      "msg": "Bucket is required",
      "path": "bucket",
      "location": "body"
    },
    {
      "type": "field",
      "msg": "Bucket name must be string",
      "path": "bucket",
      "location": "body"
    }
  ]
}
```

Error konten

```json
{
  "status": false,
  "msg": "Error: Hanya menerima gambar atau PDF"
}
```
