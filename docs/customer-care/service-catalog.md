---
sidebar_position: 3
description: DB-driven service catalog and the per-item validation rule engine
---

# Service Catalog & Rule Engine

:::tip[Penyusun]

- Bazrira Noerfirdiansyah :: Fullstack Developer Senior Associate - Operational Technology

:::

The catalog replaces the old hard-coded `arr_renovasi` / `arr_perbaikan` / `arr_lainnya`
arrays with a normalized, four-level hierarchy stored in the database:

```
custcare_service_category
  └─ custcare_service
       └─ custcare_service_group
            └─ custcare_service_item   ──(many-to-many via item_rule)──  custcare_validation_rule
```

Source: `services/v1/customer_care/cc_catalog.service.js` and
`services/v1/customer_care/cc_validation.evaluator.js`. Wiring:
`controllers/v1/customer_care/cc_catalog.controller.js` (`GetServiceCategories`,
`GetGroupedSubServices`) via `routes/v1/customer_care/cc_catalog.route.js`.

:::note Route move
These two endpoints used to sit under the `/document-request/*` prefix. They were pulled out
of `permintaan_izin` into the dedicated catalog module and now mount at the **top level**
(`/get-service-category`, `/get-sub-service-grouped`) under both `/api/v1` and `/api/v2`. The
old thin wrapper `getGroupedSubServiceAppointment` in `permintaan_izin.service.js` was removed.
:::

:::note Image URLs
`image_url` values are resolved through `buildImageUrl(filename)`: a value already starting
with `http` is returned as-is; otherwise it is prefixed to `${ASSETS_URL}/img/cc/{filename}`.
`null`/empty stays `null`.
:::

## `POST /get-service-category`

Returns the active categories, each with its nested active services. Used to render the
top-level perizinan menu. Source: `getServiceCategories` in `cc_catalog.service.js`.

### Request

| Where | Field | Required |
| ----- | ----- | -------- |
| Header | `x-auth-token` | yes |

### Response

```json
{
  "success": true,
  "msg": "OK",
  "data": [
    {
      "id": 1,
      "code": "PERIZINAN",
      "name": "Perizinan",
      "description": "...",
      "image_url": "https://.../img/cc/perizinan.png",
      "screen": null,
      "modal": false,
      "is_active": true,
      "show": true,
      "sort_order": 0,
      "services": [
        {
          "id": 10,
          "code": "RENOVASI",
          "name": "Renovasi",
          "description": "...",
          "image_url": "https://.../img/cc/renovasi.png",
          "screen": null,
          "modal": false,
          "is_active": true,
          "show": true,
          "sort_order": 0
        }
      ]
    }
  ]
}
```

`is_active` is derived from the row's `status === 1`. Categories come from
`CcServiceCategory.findActive()` (ordered by `sort_order`); services from
`CcService.findByCategory(categoryId)`.

## `POST /get-sub-service-grouped`

Returns the **groups** and **items** under one service, with each item already evaluated by
the rule engine so the client knows whether it is selectable.

Source: `getGroupedSubServices` in `cc_catalog.service.js`, called through
`getGroupedSubServicesSafe` (a defensive wrapper that logs and swallows engine errors so a
rule-evaluation failure never 500s the menu).

### Request

| Where | Field | Required | Notes |
| ----- | ----- | -------- | ----- |
| Header | `x-auth-token` | yes | |
| Body | `service_code` | yes | e.g. `RENOVASI`. Missing → `service_code diperlukan`. |

```json
{ "service_code": "RENOVASI" }
```

### Response

```json
{
  "success": true,
  "msg": "OK",
  "data": {
    "service": {
      "id": 10,
      "code": "RENOVASI",
      "name": "Renovasi",
      "legacy_service_code": "renovasi"
    },
    "groups": [
      {
        "id": 100,
        "code": "STRUKTURAL",
        "name": "Pekerjaan Struktural",
        "description": "...",
        "working_days": 4,
        "lead_finish_days": 8,
        "sort_order": 0,
        "items": [
          {
            "id": 1000,
            "code": "PERLUASAN",
            "name": "Perluasan Bangunan",
            "description": "...",
            "image_url": null,
            "screen": null,
            "modal": false,
            "is_active": true,
            "show": true,
            "disabled_reason": null,
            "flow_type": "NEW",
            "need_tnc": true,
            "sort_order": 0
          }
        ]
      }
    ]
  }
}
```

Per item, `is_active` is the **rule-engine result** (`enabled`), and `disabled_reason`
explains why an item is greyed out when `is_active` is `false`. `flow_type` (default `NEW`)
tells the client which downstream flow to launch.

`need_tnc` (commit `28bc523`) is `true` when the item's own `code` matches an active
`terms_and_conditions.service_code` row scoped to `client_type = DOCUMENT_PERMISSION`. It lets the
client know **upfront** whether a Terms & Conditions step is required before starting the form,
without a separate probe call. The flag is resolved by `resolveNeedTncByItemId`, which batches the
lookup across the whole item set (one `Op.in` query) to avoid N+1 queries during assembly.

:::note `items[].code` is the form key
When the member picks an enabled item, the client passes that item's `code` as the `form_code`
to [`/perizinan/form/start`](./form-wizard.md#post-perizinanformstart). The engine resolves the
item's `code` → form via `custcare_form.service_item_id` (`resolveFormByCode`, migration 028),
so the client-facing catalog code is decoupled from the form's own internal `code`. E.g. the
renovasi permit item `PENGAJUAN_IZIN_RENOVASI` and the extension item
`PERPANJANGAN_IZIN_RENOVASI` (whose form's internal code stays `PERPANJANGAN_RENOVASI`).
:::

Performance note: item rules are batch-loaded for all items in one query via
`CcServiceItemRule.findRulesForItems(itemIds)` (a join to `custcare_validation_rule`),
keyed by `item_id`, to avoid N+1 lookups during assembly.

## The validation rule engine

Each item can have zero or more rules attached (`custcare_service_item_rule`). Rules are
evaluated **in `sort_order`** by `evaluateRules(rules, ctx)`; the engine **short-circuits on
the first failing rule** and returns that rule's message.

```js
// cc_validation.evaluator.js (simplified)
async function evaluateRules(rules, ctx) {
  for (const rule of rules) {
    const evaluator = evaluators[rule.type];
    if (!evaluator) continue;                 // unknown type is skipped (treated as pass)
    const result = await evaluator(ctx, rule.params || {});
    if (!result.passed) {
      return {
        enabled: false,
        disabled_reason:
          rule.message_override || result.message || rule.default_message,
      };
    }
  }
  return { enabled: true, disabled_reason: null };
}
```

The disabled message precedence is: **`message_override`** (per item-rule link) →
**evaluator's own `message`** → **`default_message`** (on the rule).

The evaluation `ctx` is `{ member, fetchOutstanding }`, where `fetchOutstanding(member_ipl)`
wraps `fetchOutstandingGeneral` and returns the parsed `Tunggakan` (0 on any error).

### Rule types

| `type` | Passes when… | `params` | Notes |
| ------ | ------------ | -------- | ----- |
| `ACTIVE_IPL` | `member.member_ipl` is set. | — | Message: `Document Permit hanya untuk IPL yang telah teraktivasi.` |
| `NO_OUTSTANDING_BILL` | `tunggakan <= 0` **or** `member_right == 1`. | — | **Fails open**: if billing is unreachable it returns `passed: true` so a billing outage doesn't block users. Fails closed only if IPL is inactive. |
| `HAS_ACTIVE_PERMIT` | The member already has a permit in `permit_codes` with a status in `statuses`. | `{ permit_codes: string[], statuses: number[] }` | Joins `appointment_member` → `appointment_services`. Empty params → fail with `Konfigurasi rule tidak lengkap.` |
| `NO_ACTIVE_PERMIT` | The member does **not** have such a permit (inverse of above). | `{ permit_codes: string[], statuses: number[] }` | Used to block duplicate requests (e.g. one open renovation at a time). |

`HAS_ACTIVE_PERMIT` / `NO_ACTIVE_PERMIT` run the same query and only differ in the pass
condition (`rows.length > 0` vs `rows.length === 0`):

```sql
SELECT am.id
FROM appointment_member am
JOIN appointment_services asvc ON asvc.id = am.service_id
WHERE am.member_id     = :member_id
  AND asvc.service_code IN (:permit_codes)
  AND am.status         IN (:statuses)
LIMIT 1
```

## Catalog data model

All tables use `freezeTableName: true` and `underscored: true`. `status = 1` means active.

### `custcare_service_category`

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK, auto-increment |
| `code` | STRING(64) | NOT NULL, unique |
| `name` | STRING(128) | NOT NULL |
| `description` | TEXT | |
| `image_url` | STRING(256) | |
| `screen` | STRING(128) | client screen hint |
| `modal` | BOOLEAN | default `false` |
| `show` | BOOLEAN | default `true` |
| `sort_order` | INTEGER | default `0` |
| `status` | SMALLINT | default `1` |

Finder: `findActive()` → all `status:1` ordered by `sort_order`.

### `custcare_service`

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK |
| `category_id` | INTEGER | NOT NULL → category |
| `code` | STRING(64) | NOT NULL, unique |
| `name` | STRING(128) | NOT NULL |
| `description` | TEXT | |
| `legacy_service_code` | STRING(64) | bridges to the legacy formulir pipeline |
| `client_type` | STRING(64) | |
| `image_url` | STRING(256) | |
| `screen` | STRING(128) | |
| `modal` | BOOLEAN | default `false` |
| `show` | BOOLEAN | default `true` |
| `sort_order` | INTEGER | default `0` |
| `status` | SMALLINT | default `1` |

Finders: `findByCategory(categoryId)`, `findByCode(code)`.

### `custcare_service_group`

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK |
| `service_id` | INTEGER | NOT NULL → service |
| `code` | STRING(64) | NOT NULL |
| `name` | STRING(128) | NOT NULL |
| `description` | TEXT | |
| `working_days` | INTEGER | default `4` |
| `lead_finish_days` | INTEGER | default `8` |
| `sort_order` | INTEGER | default `0` |
| `status` | SMALLINT | default `1` |

Finder: `findByService(serviceId)`.

### `custcare_service_item`

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK |
| `group_id` | INTEGER | NOT NULL → group |
| `code` | STRING(64) | NOT NULL |
| `name` | STRING(128) | NOT NULL |
| `description` | TEXT | |
| `image_url` | STRING(256) | |
| `screen` | STRING(128) | |
| `modal` | BOOLEAN | default `false` |
| `show` | BOOLEAN | default `true` |
| `flow_type` | STRING(16) | default `NEW` |
| `sort_order` | INTEGER | default `0` |
| `status` | SMALLINT | default `1` |

Finder: `findByGroup(groupId)`.

### `custcare_service_item_rule`

Join table linking an item to a validation rule, with an optional per-link message override.

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK |
| `item_id` | INTEGER | NOT NULL → item |
| `rule_id` | INTEGER | NOT NULL → rule |
| `message_override` | TEXT | overrides the rule's `default_message` for this item |
| `sort_order` | INTEGER | default `0` — evaluation order |

Finder: `findRulesForItems(itemIds)` → batch join keyed by `item_id`.

### `custcare_validation_rule`

| Column | Type | Notes |
| ------ | ---- | ----- |
| `id` | INTEGER | PK |
| `code` | STRING(64) | NOT NULL, unique |
| `type` | STRING(64) | NOT NULL — maps to an evaluator (see rule types above) |
| `params` | JSONB | default `{}` — e.g. `{ permit_codes, statuses }` |
| `default_message` | TEXT | NOT NULL — shown when the rule fails |
| `status` | SMALLINT | default `1` |
