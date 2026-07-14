---
sidebar_position: 7
description: What changed since the first perizinan revamp cut (bb10526 → 17a82f5)
---

# Changelog

:::tip[Penyusun]

- Bazrira Noerfirdiansyah :: Fullstack Developer Senior Associate - Operational Technology

:::

The initial version of this documentation covered commits `5a86a69` → `bb10526`. This page
records the deltas added since, so the rest of the docs stay authoritative. Newest batch on top.

- **`2c1c050` → `17a82f5`** — see [Since `2c1c050`](#since-2c1c050--17a82f5) below.
- **`cad44d6` → `2c1c050`** — the 7-commit batch documented from [§1](#1-endpoints-moved-to-the-perizinan-domain) onward.

All other pages have been updated to reflect this end state.

## Since `2c1c050` → `17a82f5`

Six further commits landed on `revamp-perizinan` after the batch below. Each is now reflected in
the relevant page:

- **`55816d0`** — `POST /perizinan/get-terms-and-conditions` accepts an optional `service_item_code`
  (the catalog `items[].code`); when no explicit `service_code` is sent, it derives one from the
  item's form `legacy_service_code`. See [Eligibility & T&C](./eligibility-and-tnc.md#lookup-order).
- **`e85825c`** — gate-document signed URLs are now built from `config.node_url` (not the inbound
  request's proto/host headers), so links are stable regardless of host headers. See
  [Form Wizard → Signed-URL capability model](./form-wizard.md#signed-url-capability-model).
- **`4bf4140`** — new unified `POST /get-my-tickets` list across complaints, pengkinian data, access
  card, and perizinan submissions. See [Riwayat Pengajuan (My Tickets)](./tickets.md).
- **`28bc523`** — `/get-sub-service-grouped` items now carry `need_tnc: boolean`, telling the client
  upfront whether a T&C step applies. See [Service Catalog](./service-catalog.md#post-get-sub-service-grouped).
- **`17a82f5`** — (a) new read-only `POST /perizinan/form/detail` (the perizinan ticket detail); and
  (b) a full `DATE` / `DATE_RANGE` validation engine (`cc_date_window.evaluator.js`, business-day
  date-math in `utils/date.util.js`). See
  [Form Wizard → `form/detail`](./form-wizard.md#post-perizinanformdetail) and
  [Date fields validation](./form-wizard.md#date-fields-validation-date--date_range).

## 1. Endpoints moved to the `/perizinan` domain

The perizinan endpoints were pulled out of the legacy `/document-request` namespace into a
dedicated `/perizinan` domain, and the catalog endpoints to the top level. **The old
`/document-request/*` paths for these are removed.**

| Old (removed) | New |
| ------------- | --- |
| `POST /document-request/check-eligibility` | `POST /perizinan/check-eligibility` |
| `POST /document-request/get-terms-and-conditions` | `POST /perizinan/get-terms-and-conditions` |
| `POST /document-request/form/start` | `POST /perizinan/form/start` |
| `POST /document-request/form/step` | `POST /perizinan/form/step` |
| `POST /document-request/form/step/submit` | `POST /perizinan/form/step/submit` |
| `POST /document-request/form/upload` | `POST /perizinan/form/upload` (still legacy/deprecated) |
| `POST /document-request/form/submit` | `POST /perizinan/form/submit` |
| `POST /document-request/form/my-submissions` | `POST /perizinan/form/my-submissions` |
| `POST /document-request/get-service-category` | `POST /get-service-category` (top-level) |
| `POST /document-request/get-sub-service-grouped` | `POST /get-sub-service-grouped` (top-level) |
| *(new)* | `GET /perizinan/public/form/document?token=…` (public gate-doc download) |

Owning files moved too: eligibility/T&C to `controllers/v1/perizinan/perizinan.controller.js`
+ `services/v1/perizinan/perizinan.service.js`; catalog to
`controllers/v1/customer_care/cc_catalog.controller.js`; the form engine controller stays at
`controllers/v1/customer_care/cc_form.controller.js`. `permintaan_izin` now serves **only**
legacy appointment/formulir flows. (Commits `cad44d6`, `78afb06`, `150db14`, `2c1c050`.)

## 2. Two new tables

- **`custcare_form_step_document`** — DB-driven descriptors for `DOWNLOAD_GATE` documents
  (`STATIC` vs `GENERATED`, `file_path`, `generator_key`). Replaces the hardcoded gate-doc list.
- **`custcare_perpanjangan_renovasi`** — typed projection table for the perpanjangan (extension)
  form, alongside `custcare_perizinan_renovasi`.

See the [Data Model](./data-model.md). (Commit `1134e96`.)

## 3. DB-driven gate documents + signed-URL download

`DOWNLOAD_GATE` steps now read their document list from `custcare_form_step_document`
(with a `legacyGateDescriptors` fallback). Documents are exposed as **signed capability
URLs** (AES token carrying `{sid, code, exp}`, 24h TTL) pointing at the public
`GET /perizinan/public/form/document`, which renders the PDF on demand — no blob persistence.
See [DOWNLOAD_GATE steps](./form-wizard.md#download_gate-steps). (Commits `f93d7a8`, `605deba`.)

## 4. Perpanjangan carryover

The extension form prefills from the member's most recent SUBMITTED `PENGAJUAN_IZIN_RENOVASI`
submission via `prefill_source = "renovasi.<code>"` (`buildCarryoverContext`), with work-scope
codes mapped to label rows (`mapLingkupLabels`) and a `loadOwner`-based Data Pemilik prefill.
See [Perpanjangan carryover](./form-wizard.md#perpanjangan-carryover). (Commit `f93d7a8`.)

## 5. Engine hardening (server-authoritative)

Readonly fields are computed and persisted server-side (`resolveReadonlyValues`); single
`CHECKBOX` agreements must be explicitly `true`; resolver-only-hidden fields are dropped from
the payload; and `form/submit` re-validates **all** steps. See
[Engine hardening](./form-wizard.md#engine-hardening--server-authoritative-rules). (Commit `f93d7a8`.)

## 6. Form-code rename & catalog linkage

`custcare_form.code` for the renovasi form is now `PENGAJUAN_IZIN_RENOVASI` (was
`PERIZINAN_RENOVASI`, migration 027). `form/start` accepts the **catalog item code** and
resolves the form through `custcare_form.service_item_id` (`resolveFormByCode`, migration 028),
so the client-facing code is decoupled from the form's internal code (e.g. the extension item
`PERPANJANGAN_IZIN_RENOVASI` → form `PERPANJANGAN_RENOVASI`).

## 7. New PDF templates

`engines/htmlPdfTemplate/perizinan/form-pengajuan-izin-renovasi.ejs` and
`surat-pemberitahuan-pekerjaan-dan-koordinasi-lingkungan.ejs`, used as `GENERATED` gate-doc
sources. (Commit `f93d7a8`.)
