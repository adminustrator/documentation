---
sidebar_position: 2
description: Kirim chat ke Silvia
---

# Chat

:::tip[Penyusun]

- Reza Nurfachmi :: Fullstack Developer Senior Associate - Operational Technology

:::

## Request Endpoint

```
POST https://api.onesmile.digital/api-v2/silvia/chat
```

## Headers

| Header Parameter | Type   | Description |
| ---------------- | ------ | ----------- |
| X-Auth-Token     | String | Token       |

## Body

| Body Parameter | Type   | Description                    |
| -------------- | ------ | ------------------------------ |
| message        | String | Pesan untuk Silvia dari member |

## Example responses

Status: `200`

```json
{
  "success": true,
  "msg": "Pesan ini akan dilanjutkan via WhatsApp",
  "data": "https://api.whatsapp.com/send?phone=628816888000&text=Heiho%21"
}
```

Status: `400`

```json
{
  "success": false,
  "msg": "Pesan wajib diisi",
  "data": []
}
```