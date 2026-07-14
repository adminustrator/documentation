---
sidebar_position: 2
description: check-eligibility gate and DB-driven terms & conditions
---

# Eligibility & Terms

:::tip[Penyusun]

- Bazrira Noerfirdiansyah :: Fullstack Developer Senior Associate - Operational Technology

:::

This page covers the two gates a member passes before filling in a permit form: the
**eligibility check** and the **terms & conditions** lookup.

## `POST /perizinan/check-eligibility`

Decides whether a member may proceed with a permit request, based on their **IPL**
(Iuran Pengelolaan Lingkungan) activation status and outstanding bill (`tunggakan`).

Controller: `ActionCheckEligibility` in `controllers/v1/perizinan/perizinan.controller.js`.
Source: `actionCheckEligibility` in `services/v1/perizinan/perizinan.service.js` — it reuses
`fetchOutstandingGeneral` (imported from `permintaan_izin.service.js`) for the bill lookup.

### Request

| Where | Field | Required | Notes |
| ----- | ----- | -------- | ----- |
| Header | `x-auth-token` | yes | Member auth key. |
| Body | `parent_member_id` | no | Used for the **kontraktor-on-behalf** flow — when a contractor files a permit on behalf of a client (the IPL owner). |

```json
{
  "parent_member_id": 12345
}
```

### Logic ladder

1. Reject if `x-auth-token` is missing → `Invalid Parameters`.
2. Resolve the `Member` by `member_authkey`; reject if not found → `Member tidak terdaftar`.
3. Default `member_ipl` to the caller's own IPL.
4. **If `parent_member_id` is provided** (contractor flow):
   - Load the client member (`Member` by `member_id`). Reject if missing → `Client kontraktor tidak ditemukan`.
   - Verify the caller is a registered contractor for that client via
     `MemberPicContractorReferred.findOne({ member_id, parent_member_id })`.
     Reject if missing → `Anda belum terdaftar sebagai kontrator a.n {client_name}`.
   - Use the **client's** `member_ipl` for the bill check.
5. If `member_ipl` is empty → **not eligible**, popup `Tagihan Air / IPL` /
   `Document Permit hanya untuk IPL yang telah teraktivasi.` (button `Kembali`, action `back`).
6. Regenerate the realtime bill via `generateBillRealBit(member_ipl, member_city_id)`.
   On error → failure `Gagal realbit`.
7. Read the outstanding amount via `fetchOutstandingGeneral(member_ipl)` and parse
   `Tunggakan` out of `realbit.data`.
8. Compute eligibility:

   ```js
   const eligible = tunggakan <= 0 || member.member_right == 1;
   const IPL_PAYMENT_DEEPLINK = '/iplMainScreen';
   ```

   `member_right == 1` is a **bypass** — those members are always eligible regardless of
   outstanding bills.

### The popup contract

Eligibility responses always include a `popup` object so the client can render a blocking
dialog without its own copy of the business rules. The empty popup constant is:

```js
const POPUP_EMPTY = {
  show: false, title: '', message: '',
  button_label: '', button_action: '', button_link: ''
};
```

| Field | Type | Meaning |
| ----- | ---- | ------- |
| `show` | boolean | Whether to render the popup at all. |
| `title` | string | Dialog title, e.g. `Tagihan Air / IPL`. |
| `message` | string | Body text. |
| `button_label` | string | CTA label, e.g. `Bayar Sekarang`, `Kembali`. |
| `button_action` | string | `back` (dismiss/return) or `deeplink` (navigate). |
| `button_link` | string | Deeplink target when action is `deeplink` (e.g. `/iplMainScreen`). |

### Response — eligible

```json
{
  "success": true,
  "msg": "success",
  "data": {
    "eligible": true,
    "popup": { "show": false, "title": "", "message": "", "button_label": "", "button_action": "", "button_link": "" }
  }
}
```

### Response — not eligible (outstanding bill)

```json
{
  "success": true,
  "msg": "success",
  "data": {
    "eligible": false,
    "popup": {
      "show": true,
      "title": "Tagihan Air / IPL",
      "message": "Mohon lakukan pembayaran tagihan Air/IPL terlebih dahulu untuk dapat melanjutkan.",
      "button_label": "Bayar Sekarang",
      "button_action": "deeplink",
      "button_link": "/iplMainScreen"
    }
  }
}
```

:::note
`success: true` with `eligible: false` is the normal "not eligible" case — `success: false`
is reserved for hard errors (bad params, member not found, realbit failure).
:::

---

## `POST /perizinan/get-terms-and-conditions`

Returns the active terms & conditions for a service. It first looks for a
**service-specific** record, then falls back to a **global** one (`service_code = null`).

Controller: `ActionGetTermsAndConditions` in `controllers/v1/perizinan/perizinan.controller.js`.
Source: `actionGetTermsAndConditions` in `services/v1/shared.service.js`.

:::note Refactor history
The handler originally lived in `permintaan_izin.service.js` (commit `b64678d`), was
extracted into the shared `shared.service.js` (commit `79d7da9`) so other domains can reuse
it, and its route was later moved off the `/document-request` prefix into the dedicated
`/perizinan` domain. The shared version also enforces the `x-auth-token` / member check.
:::

### Request

| Where | Field | Required | Default | Notes |
| ----- | ----- | -------- | ------- | ----- |
| Header | `x-auth-token` | yes | — | Member auth key. |
| Body | `service_code` | no | — | When set, the lookup prefers the matching service-specific T&C. Wins over `service_item_code`. |
| Body | `service_item_code` | no | — | The catalog **item** code (`items[].code` from `/get-sub-service-grouped`). When supplied **without** an explicit `service_code`, the service derives `service_code` from the item's form (see below). |
| Body | `client_type` | no | `DOCUMENT_PERMISSION` | Scopes the lookup. |

```json
{
  "service_code": "RENOVASI",
  "client_type": "DOCUMENT_PERMISSION"
}
```

```json
{
  "service_item_code": "PENGAJUAN_IZIN_RENOVASI",
  "client_type": "DOCUMENT_PERMISSION"
}
```

### Lookup order

1. **`service_item_code` resolution** (commit `55816d0`): when `service_item_code` is given and no
   explicit `service_code`, the perizinan service resolves the item's form via `resolveFormByCode`
   (the same form-resolution used by `form/start`) and derives `service_code` from the form's
   `legacy_service_code` (e.g. the renovasi item → form → `'renovasi'`). It then defers to the shared
   lookup with that derived `service_code`. An explicit `service_code` always wins, so the legacy
   `agreement_link` flow is unaffected.
2. If `service_code` is provided (or was derived above): `TermsAndConditions.findOne({ client_type, service_code, is_active: true })`.
3. Fallback: `TermsAndConditions.findOne({ client_type, service_code: null, is_active: true })`.
4. None found → `{ success: false, msg: 'Terms and conditions not found', data: {} }`.

:::note Ownership moved
The `service_item_code`-aware wrapper `actionGetTermsAndConditions` now lives in
`services/v1/perizinan/perizinan.service.js` (delegating to `services/v1/shared.service.js` for the
generic `(client_type, service_code)` lookup). The derivation reads `legacy_service_code` from the
**form**, not from `custcare_service` — the T&C rows are keyed by the form's code (`'renovasi'`),
whereas the parent service's `legacy_service_code` is `'izinkerja'`.
:::

### Response

```json
{
  "success": true,
  "msg": "OK",
  "data": {
    "version": 1,
    "title": "Syarat & Ketentuan Renovasi",
    "sections": [],
    "agreement": {}
  }
}
```

`sections` and `agreement` are stored as JSONB, so their internal shape is defined by the
content seeded into the table rather than the API.

### `terms_and_conditions` table

Source: `models/termsAndConditions.model.js` (`freezeTableName: true`, no `createdAt`/`updatedAt`).

| Column | Type | Constraints | Notes |
| ------ | ---- | ----------- | ----- |
| `id` | INTEGER | PK, auto-increment | Cast to int via getter. |
| `client_type` | STRING | NOT NULL | e.g. `DOCUMENT_PERMISSION`. |
| `service_code` | STRING | nullable | `null` = global fallback record. |
| `version` | INTEGER | NOT NULL, default `1` | Bump when content changes. |
| `title` | STRING | NOT NULL | Display title. |
| `sections` | JSONB | NOT NULL, default `[]` | T&C body sections. |
| `agreement` | JSONB | nullable | Agreement / checkbox config. |
| `is_active` | BOOLEAN | NOT NULL, default `true` | Only active rows are served. |
| `created_date` | DATE | NOT NULL | |
| `created_by` | STRING | nullable | |
