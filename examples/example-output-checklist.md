

---

## ⚠️ Source limitation

Gate2way documentation was not provided. The checklist is based **only** on the task description:
- bug report about a double `blikcode` input screen for `blik-InstaX`;
- field mapping note: `source.bankCode` (for `blik-MB`) vs `source.accountBankCode` (for `blik-InstaX`);
- provider status `PaymentPageRequested` → internal `AWAITING_REDIRECT`;
- internal status `INPUT_REQUIRED`;
- parameter `additionalParameters.blikcode`;
- terminal parameter `paymentMethod`;
- reference payment `067d7799ef1946dd8945de0416bffa68`.

Anything else (HTTP codes, error codes, signature, currencies, webhooks, refund/withdrawal, etc.) is **TBC** and intentionally omitted from test cases to avoid hallucination.

---

## Part 1 — Provider & Task Summary

**Gate2way**, payment method **BLIK** (schemes `blik-MB` and `blik-InstaX`), operation **DEPOSIT**. The flow supports two scenarios: (1) `blikcode` is entered on our side and forwarded to the provider, or (2) the client is redirected to the provider's payment page when `additionalParameters.blikcode` is not supplied and the provider responds with `PaymentPageRequested`. The key provider-specific trait: `blikcode` maps to **different fields** depending on the scheme — `source.bankCode` for `blik-MB` and `source.accountBankCode` for `blik-InstaX`. The fix in scope eliminates a defect where, for `blik-InstaX` without `additionalParameters.blikcode`, the user sees the blikcode input screen twice (our `INPUT_REQUIRED`, then the provider's `AWAITING_REDIRECT`).

---

## Part 2 — Provider-Specific Test Checklist (in-scope only)

| ID | Title | Section | Priority | Steps | Expected Result | Why provider-specific |
|---|---|---|---|---|---|---|
| BLIK-IX-01 | blik-InstaX: blikcode supplied via `additionalParameters` — mapped to `source.accountBankCode` | Field mapping | Critical | 1. Create DEPOSIT with `paymentMethod=blik-InstaX`. 2. Pass `additionalParameters.blikcode=<valid>`. 3. Capture the outbound request to the provider. | The outbound request contains `blikcode` in `source.accountBankCode` (NOT in `source.bankCode`). Provider returns a terminal status without redirect. | Task explicitly states that `blik-InstaX` uses `source.accountBankCode`, which differs from `blik-MB`. |
| BLIK-IX-02 | blik-MB: blikcode supplied — mapped to `source.bankCode` (regression) | Field mapping | Critical | 1. Create DEPOSIT with `paymentMethod=blik-MB`. 2. Pass `additionalParameters.blikcode=<valid>`. 3. Capture the outbound request. | The outbound request contains `blikcode` in `source.bankCode`. `blik-MB` behaviour is not broken by the fix. | Same task — sister scheme using `source.bankCode`; regression coverage is required when changing the mapping logic. |
| BLIK-IX-03 | blik-InstaX without `additionalParameters.blikcode` — NO double input screen | Flow / UX | Critical | 1. Create DEPOSIT with `paymentMethod=blik-InstaX`. 2. Do NOT pass `additionalParameters.blikcode`. 3. Walk the flow to a final state. | The user sees **only one** blikcode input screen: either ours (`INPUT_REQUIRED`) or the provider's (`AWAITING_REDIRECT` after `PaymentPageRequested`), never both in sequence. The defect from the task is not reproduced. | Direct fix case: reference payment `067d7799ef1946dd8945de0416bffa68` shows the sequence `INPUT_REQUIRED → AWAITING_REDIRECT`. |
| BLIK-IX-04 | blik-InstaX without `additionalParameters.blikcode`: correct flow routing | Flow routing | High | 1. Create DEPOSIT with `paymentMethod=blik-InstaX` and no `blikcode`. 2. Record the provider response. | If the provider returns `PaymentPageRequested` + `Links` — the system goes to `AWAITING_REDIRECT` **without** a preceding `INPUT_REQUIRED`. If the provider returns any other status — the system goes to `INPUT_REQUIRED` without a subsequent redirect. The two paths are mutually exclusive. | The task identifies the coexistence of `INPUT_REQUIRED` and `AWAITING_REDIRECT` as the defect; correct routing must be exclusive. |
| BLIK-IX-05 | blik-InstaX: provider status `PaymentPageRequested` → `AWAITING_REDIRECT` | Status mapping | High | 1. Receive a provider response with status `PaymentPageRequested` and `Links` for redirect. 2. Verify internal status and behaviour. | Internal status becomes `AWAITING_REDIRECT`. The redirect URL is taken from the `Links` array. The client is correctly redirected. | Task explicitly states the provider returned `PaymentPageRequested` and the redirect happened "probably due to data in the `Links` array of the provider response". |
| BLIK-IX-06 | blik-InstaX: blikcode supplied but invalid/expired | Negative | High | 1. DEPOSIT `blik-InstaX` with a deliberately invalid `additionalParameters.blikcode`. 2. Wait for the provider response. | The transaction does not enter the double-input flow. Mapping still goes to `source.accountBankCode`. The final status reflects the provider's error (specific code — TBC, no documentation). | Verifies the new mapping under a negative scenario — specific to `blik-InstaX`. |
| BLIK-IX-07 | Scheme switch: `blik-MB` → `blik-InstaX` on the same terminal | Configuration | Medium | 1. On the terminal, switch `paymentMethod` from `blik-MB` to `blik-InstaX`. 2. Create DEPOSIT with `blikcode`. | Mapping switches from `source.bankCode` to `source.accountBankCode` with no other changes required. | Task records the scenario change "moved off the default BLIK -> blik-MB flow" via the terminal `paymentMethod` parameter. |
| BLIK-IX-08 | blik-InstaX: `Links` missing in response when status is `PaymentPageRequested` | Edge / Negative | Medium | 1. Simulate a provider response with `PaymentPageRequested` but no `Links`. | The system does not move to `AWAITING_REDIRECT` with an empty URL; the case is handled deterministically as an error / TBC per provider docs. Behaviour is not undefined. | The task ties the redirect to the presence of data in `Links`; the inverse case is not documented and requires deterministic handling. |

---

## Part 3 — Provider Reference Sheet

### 3a. HTTP Status Codes

TBC — documentation not provided.

### 3b. Error Codes / Failure Reasons

TBC — documentation not provided. The task description does not mention any error codes.

### 3c. Amounts & Currency

- amount format: TBC
- currency format: TBC
- restrictions: TBC
- auto-conversion: TBC
- special rules: TBC

(BLIK is a Polish payment method, typically PLN, but this is **not confirmed** by the task and is intentionally not asserted without a source.)

### 3d. Payment Statuses

| Status | Type | Description | Triggers Webhook | Can transition to |
|---|---|---|---|---|
| `PaymentPageRequested` (provider) | Intermediate | The provider requested its own payment page; the response contains a `Links` array. | TBC | Internal `AWAITING_REDIRECT` |
| `INPUT_REQUIRED` (internal) | Intermediate | Awaiting `blikcode` input on our side. | TBC | `AWAITING_REDIRECT` (defective path — must be eliminated) / a final status after input |
| `AWAITING_REDIRECT` (internal) | Intermediate | Awaiting the client's return from the provider's payment page. | TBC | Terminal status — TBC |

All other provider statuses and their webhook-trigger behaviour — TBC.

---

## Part 5 — Confluence Summary Table

| Item | Description | Value |
|---|---|---|
| **General Info** | | |
| Integration type | — | Gate2way (type — TBC) |
| Provider | — | Gate2way |
| **Operations** | | |
| Deposit | In task scope | Yes (BLIK: `blik-MB`, `blik-InstaX`) |
| Refund | — | TBC |
| Withdrawal | — | TBC |
| Recurring | — | TBC |
| Check-state | — | TBC |
| Pre-auth | — | TBC |
| Capture | — | TBC |
| Void | — | TBC |
| **Payment Methods** | | |
| BASIC_CARD | — | TBC |
| APMs | In scope | BLIK (`blik-MB`, `blik-InstaX`) |
| **PSP Flow** | | |
| Endpoint | — | TBC |
| Finalization source | — | TBC (likely webhook + redirect, not confirmed) |
| Terminal statuses | — | TBC |
| **Webhook** | | |
| yes/no | — | TBC |
| static/dynamic | — | TBC |
| operations covered | — | TBC |
| signature formula | — | TBC |
| **Security** | | |
| auth method | — | TBC |
| signature algorithm | — | TBC |
| token levels | — | TBC |
| **Amount / Currency** | | |
| format | — | TBC |
| conversion | — | TBC |
| **Additional Notes** | Quirks | `blikcode` is mapped to different fields per scheme: `source.bankCode` for `blik-MB`, `source.accountBankCode` for `blik-InstaX`. When `additionalParameters.blikcode` is omitted and the provider responds with `PaymentPageRequested`, the client is redirected to the provider's page via `Links`. Coexistence of internal `INPUT_REQUIRED` and `AWAITING_REDIRECT` is a defect — the two paths must be mutually exclusive. Reference payment: `067d7799ef1946dd8945de0416bffa68`. |

---

## Recommendation

To get full coverage (HTTP codes, error mapping, signature, webhook statuses, currencies, other operations), please share Gate2way documentation — text, link, or PDF. Currently **9+ fields in the Confluence table are TBC**, and without a source it would be unsafe to fill them in or to expand the test cases.
