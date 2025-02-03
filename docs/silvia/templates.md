---
sidebar_position: 1
description: Template chat untuk Silvia
---

# Templates

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Request Endpoint

```
GET https://api.onesmile.digital/api-v2/silvia/templates
```

## Headers

| Header Parameter | Type   | Description |
| ---------------- | ------ | ----------- |
| X-Auth-Token     | String | Token       |

## Example responses

Status: `200`

```json
{
  "success": true,
  "msg": "Berhasil",
  "data": [
    {
      "id": 1,
      "message": "Halo, Silvia!"
    },
    {
      "id": 3,
      "message": "Halo Silvia, saya butuh bantuan."
    },
    {
      "id": 4,
      "message": "Halo Silvia, saya ada pertanyaan."
    },
    {
      "id": 5,
      "message": "Halo Silvia, saya ada error. Apakah bisa dibantu?"
    }
  ]
}
```
