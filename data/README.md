# Voice360 Synthetic Data

All records in this directory are fictional and exist only for the capstone demonstration.

## Files

- `customers.csv`: verification and customer-session fields.
- `claims.csv`: customer-authorized claim-status fields.
- `callbacks.csv`: callback action target.
- `test-cases.csv`: UAT execution sheet.

## Import guidance

Preferred option: import the first three CSV files into separate Dataverse tables. For a low-concurrency demo, they may instead be converted into named Excel tables in a workbook stored in OneDrive or SharePoint.

Do not upload customer, claim, or callback data as a Copilot Studio knowledge source. Knowledge-file contents can be available to every agent user. Access these records through agent flows after verification.

## Customer fields

| Field | Type | Description |
|---|---|---|
| `customer_id` | Text/key | Opaque fictional customer reference |
| `first_name` | Text | First name returned only after verification |
| `last_name` | Text | Not required in the conversational response |
| `policy_number` | Text/alternate key | Normalized policy identifier |
| `date_of_birth` | Date | Fictional verification value |
| `registered_phone_last4` | Text | Four digits; keep as text to preserve leading zeroes |
| `preferred_language` | Text | Reserved for future multilingual support |
| `policy_status` | Choice/text | Only `Active` records can pass mock verification |

## Claim fields

| Field | Type | Description |
|---|---|---|
| `claim_number` | Text/key | Fictional claim identifier |
| `customer_id` | Lookup/text | Owner used in authorization filter |
| `policy_number` | Text | Associated policy |
| `claim_type` | Text | Customer-safe claim type |
| `incident_date` | Date | Fictional incident date |
| `filed_date` | Date | Fictional submission date |
| `status` | Choice/text | Approved display status |
| `status_explanation` | Multiline text | Customer-safe explanation |
| `missing_documents` | Text | Semicolon-delimited list or blank |
| `next_action` | Multiline text | Recorded customer-safe next step |
| `expected_update_date` | Date | Optional estimate |
| `last_updated` | Date | Latest recorded update |
| `escalation_eligible` | Boolean | Demo routing flag; not a customer-facing fact |

## Callback fields

| Field | Type | Description |
|---|---|---|
| `callback_reference` | Text/key | Unique request reference |
| `customer_id` | Lookup/text | Verified customer |
| `claim_number` | Text | Optional authorized claim |
| `callback_reason` | Text | Sanitized short reason |
| `preferred_date` | Date | Requested date |
| `preferred_window` | Choice/text | One approved window |
| `masked_phone` | Text | Masked registered phone only |
| `status` | Choice/text | `Open`, `Completed`, or `Cancelled` |
| `created_at` | Date/time | UTC ISO timestamp |
| `correlation_id` | Text | Safe trace identifier |

## Data reset

Before a recorded demo, remove newly created callback rows or choose a new date/window so the duplicate test does not interfere with the happy path. Preserve the seeded `CB-20260814-0999` row for the duplicate callback test.

