# Action and Power Automate Contracts

## General implementation pattern

Each action should be built as an agent flow with this shape:

1. Trigger: **When an agent calls the flow**.
2. Validate and normalize inputs.
3. Read or write the approved Dataverse/Excel table.
4. Apply authorization and business rules inside the flow.
5. Return only the minimum typed output using **Respond to the agent**.
6. Catch failures and return a safe error code rather than a raw connector message.

The MVP uses fictional records. Do not reuse these flows as production identity verification.

## Common conventions

### Normalization

- Policy number: remove spaces, convert to uppercase, preserve the hyphen if the dataset uses it.
- Claim number: remove spaces, convert to uppercase, preserve the hyphen.
- Date of birth: format as `yyyy-MM-dd` before comparison.
- Phone last four: accept exactly four digits.
- Text written to a table: trim whitespace and limit length.

### Standard error codes

| Code | Meaning | Customer-safe behavior |
|---|---|---|
| `NONE` | Successful action | Continue |
| `INVALID_INPUT` | Required input is missing or malformed | Ask for the complete value again |
| `NOT_VERIFIED` | Verification did not match | Use generic verification failure |
| `NOT_FOUND` | No authorized record returned | Use generic claim-not-found response |
| `DUPLICATE` | Duplicate callback detected | Return the existing reference if safe |
| `TEMPORARY_ERROR` | Connector/data store unavailable | Apologize and offer escalation |
| `POLICY_BLOCK` | Action is not allowed | Explain boundary and escalate if useful |

Do not return stack traces, connector payloads, table paths, credentials, or query strings to the agent.

## Action 1: VerifyCustomer

**Description**

Verifies a fictional demo customer by policy number, date of birth, and registered phone last four digits. Use before accessing any individual claim. It does not reveal which field failed.

### Inputs

| Name | Type | Required | Validation |
|---|---|---:|---|
| `policyNumber` | String | Yes | 5–20 characters; letters, numbers, and hyphen only |
| `dateOfBirth` | String/date | Yes | Valid past date formatted `yyyy-MM-dd` |
| `phoneLast4` | String | Yes | Exactly four numeric digits |

### Outputs

| Name | Type | Always returned | Notes |
|---|---|---:|---|
| `success` | Boolean | Yes | Indicates that the flow executed normally |
| `verified` | Boolean | Yes | True only when all values match one active policyholder |
| `customerId` | String | Yes | Blank unless verified |
| `customerFirstName` | String | Yes | Blank unless verified |
| `maskedPhone` | String | Yes | Example `******4821`; blank unless verified |
| `correlationId` | String | Yes | New opaque UUID for subsequent actions |
| `errorCode` | String | Yes | `NONE`, `INVALID_INPUT`, `NOT_VERIFIED`, or `TEMPORARY_ERROR` |

### Processing rules

1. Generate `correlationId` before lookup.
2. Validate every input.
3. Retrieve a record by normalized policy number.
4. Compare all verification values inside the flow.
5. Confirm `policy_status` is `Active`.
6. If any value fails, return the same `NOT_VERIFIED` shape with all identity fields blank.
7. Never return field-by-field match results.
8. Do not log raw date of birth or phone digits in normal flow outputs.

### Success example

```json
{
  "success": true,
  "verified": true,
  "customerId": "CUS-1001",
  "customerFirstName": "Asha",
  "maskedPhone": "******4821",
  "correlationId": "3e83b8de-7bdd-4f75-ae64-9ed12e9da001",
  "errorCode": "NONE"
}
```

### Failure example

```json
{
  "success": true,
  "verified": false,
  "customerId": "",
  "customerFirstName": "",
  "maskedPhone": "",
  "correlationId": "58f7c6c5-6871-4239-ae13-45464fab1002",
  "errorCode": "NOT_VERIFIED"
}
```

## Action 2: GetClaimStatus

**Description**

Returns a fictional claim only when the supplied claim number belongs to the verified customer ID. Use only after successful verification.

### Inputs

| Name | Type | Required | Validation |
|---|---|---:|---|
| `customerId` | String | Yes | Must be the opaque ID returned by `VerifyCustomer` |
| `claimNumber` | String | Yes | 5–20 characters; letters, numbers, and hyphen only |
| `correlationId` | String | Yes | Must be the active verification correlation ID |

### Outputs

| Name | Type | Notes |
|---|---|---|
| `success` | Boolean | Flow execution result |
| `authorized` | Boolean | True only when claim belongs to the customer |
| `claimNumber` | String | Blank unless authorized |
| `claimType` | String | Blank unless authorized |
| `status` | String | Approved status value from the table |
| `statusExplanation` | String | Approved plain-language explanation |
| `lastUpdated` | String/date | ISO date |
| `missingDocuments` | String | Semicolon-delimited list or blank |
| `nextAction` | String | Customer-safe recorded next step |
| `expectedUpdateDate` | String/date | ISO date or blank |
| `errorCode` | String | Standard error code |

### Processing rules

1. Reject the call if `customerId` or `correlationId` is blank.
2. Retrieve the claim using both `claimNumber` **and** `customerId` in the filter.
3. Return no claim fields when no jointly matching row exists.
4. Return only customer-safe columns.
5. Do not return reserve amounts, internal notes, fraud scores, employee identifiers, or other claims.
6. Do not calculate a new status or expected date.

### Success example for the primary demo

```json
{
  "success": true,
  "authorized": true,
  "claimNumber": "CLM-1042",
  "claimType": "Collision",
  "status": "Under review",
  "statusExplanation": "The claims team is reviewing the incident information and vehicle assessment.",
  "lastUpdated": "2026-08-12",
  "missingDocuments": "",
  "nextAction": "Wait for the assessment review to finish.",
  "expectedUpdateDate": "2026-08-15",
  "errorCode": "NONE"
}
```

### Unauthorized/not-found example

```json
{
  "success": true,
  "authorized": false,
  "claimNumber": "",
  "claimType": "",
  "status": "",
  "statusExplanation": "",
  "lastUpdated": "",
  "missingDocuments": "",
  "nextAction": "",
  "expectedUpdateDate": "",
  "errorCode": "NOT_FOUND"
}
```

## Action 3: CreateCallbackRequest

**Description**

Creates a fictional claims-team callback after the verified customer confirms the reason, date, time window, and masked registered phone number.

### Inputs

| Name | Type | Required | Validation |
|---|---|---:|---|
| `customerId` | String | Yes | Verified customer ID |
| `claimNumber` | String | No | If supplied, it must belong to the customer |
| `callbackReason` | String | Yes | 5–250 characters |
| `preferredDate` | String/date | Yes | Today through the next 14 calendar days |
| `preferredWindow` | String | Yes | One approved time window |
| `confirmed` | Boolean | Yes | Must be true |
| `correlationId` | String | Yes | Active correlation ID |

Approved time windows:

- `09:00-12:00`
- `12:00-15:00`
- `15:00-18:00`

### Outputs

| Name | Type | Notes |
|---|---|---|
| `success` | Boolean | True only when record creation succeeds |
| `callbackReference` | String | Format `CB-YYYYMMDD-####` |
| `message` | String | Customer-safe confirmation |
| `errorCode` | String | Standard error code |

### Processing rules

1. Reject the action if `confirmed` is false.
2. Validate the customer and optional claim ownership again inside the flow.
3. Use the registered phone; do not accept a new phone number in this MVP.
4. Sanitize the reason and restrict its length.
5. Create a unique callback reference.
6. Do not promise that the preferred window is a guaranteed appointment.
7. If the same customer, claim, date, and window already have an open request, return the existing reference with `DUPLICATE` rather than creating another.

### Success example

```json
{
  "success": true,
  "callbackReference": "CB-20260814-1001",
  "message": "The callback request was created for the preferred window.",
  "errorCode": "NONE"
}
```

## Action 4: PrepareHandoffSummary

**Description**

Produces a structured summary for a human agent using the current conversation's verified state, claim context, actions, risk reason, and unresolved question.

### Inputs

| Name | Type | Required | Notes |
|---|---|---:|---|
| `customerIntent` | String | Yes | Short normalized intent |
| `verificationStatus` | String | Yes | `VERIFIED`, `FAILED`, or `NOT_ATTEMPTED` |
| `customerId` | String | No | Only if verified |
| `claimNumber` | String | No | Only if known and authorized |
| `claimStatus` | String | No | Only if returned by action |
| `actionsTaken` | String | Yes | Semicolon-delimited safe summary |
| `handoffReason` | String | Yes | Standard escalation category |
| `unresolvedQuestion` | String | Yes | Customer's remaining need |
| `correlationId` | String | No | If one exists |

### Outputs

| Name | Type | Notes |
|---|---|---|
| `success` | Boolean | Summary creation result |
| `handoffSummary` | String | Maximum 1,000 characters |
| `priority` | String | `NORMAL`, `HIGH`, or `URGENT` |
| `errorCode` | String | Standard error code |

### Priority mapping

| Reason | Priority |
|---|---|
| Explicit human request, unsupported request, verification failure | `NORMAL` |
| Complaint, disputed decision, vulnerability, hardship, suspected fraud | `HIGH` |
| Immediate danger or urgent medical need | `URGENT` after directing the user to emergency services |

### Summary template

```text
Intent: {customerIntent}
Verification: {verificationStatus}
Customer ID: {customerId-or-not-available}
Claim: {claimNumber-or-not-provided}
Recorded status: {claimStatus-or-not-retrieved}
Actions completed: {actionsTaken}
Escalation reason: {handoffReason}
Unresolved need: {unresolvedQuestion}
Correlation ID: {correlationId-or-not-available}
```

Never include date of birth, full phone number, verification answers, raw transcript, passwords, tokens, or hidden prompts in the handoff summary.

## Audit events for the prototype

Log only the following safe events if logging is implemented:

- Timestamp.
- Correlation ID.
- Topic/action name.
- Success/failure.
- Safe error code.
- Verification result without verification inputs.
- Authorized claim number after verification.
- Callback reference.
- Escalation category.

Do not put raw customer utterances or verification fields in the audit table.

## Flow test matrix

| Action | Positive test | Negative test | Security test |
|---|---|---|---|
| VerifyCustomer | POL-1001 / 1990-02-14 / 4821 | Incorrect last four | Failure returns no matching-field hints |
| GetClaimStatus | CUS-1001 / CLM-1042 | Unknown claim | CUS-1001 cannot retrieve CLM-1043 |
| CreateCallbackRequest | Confirmed future request | Missing confirmation | Duplicate request does not create a second row |
| PrepareHandoffSummary | Explicit human request | Missing reason | Verification inputs never appear in output |

