---
sidebar_position: 2
description: Fetch File dari Azure Storage
---

# Fetch File

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Request Endpoint

```
POST https://kube-prd.az-api.onesmile.digital/homepage/api/v2/utils/fetch
```

## Headers

| Header Parameter | Type   | Description |
| ---------------- | ------ | ----------- |
| X-Auth-Token     | String | Token       |

## Body

| Body Parameter | Type   | Description | Required |
| -------------- | ------ | ----------- | :------: |
| path           | String | Path objek  |    ✅    |

## Example responses

### Status: `200`

```json
{
  "success": true,
  "msg": "Sukses",
  "data": {
    "path": "testing/59-upload_file_72d178fc3800717b.pdf",
    "url": "https://onesmilestorageprod.blob.core.windows.net/onesmile/testing/59-upload_file_72d178fc3800717b.pdf"
  }
}
```

### Status: `400`

Error validasi

```json
{
  "status": false,
  "msg": "Bad Request",
  "errors": [
    {
      "type": "field",
      "msg": "Path is required",
      "path": "path",
      "location": "body"
    },
    {
      "type": "field",
      "msg": "Path name must be string",
      "path": "path",
      "location": "body"
    }
  ]
}
```

### Status: `404`

Error path

```json
{
  "status": false,
  "msg": "Error: File tidak ditemumkan"
}
```
