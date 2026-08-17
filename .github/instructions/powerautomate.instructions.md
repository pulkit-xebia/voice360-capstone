---
applyTo: "mock/**/*.json"
---

# Power Automate flow instructions — Voice360

## Purpose
JSON files in `mock/` are **blueprint contracts** for Power Automate agent flows. They document inputs, steps, outputs, and test cases. They are not importable flow packages.

## Required structure for every flow contract

```json
{
  "flowName": "ExactNameMatchingCopilotStudioTool",
  "description": "One sentence.",
  "trigger": "When an agent calls the flow",
  "inputs": [],
  "steps": [],
  "outputs": [],
  "testCase": {}
}
```

## Input rules
- Every input must have `name`, `type` (`string` | `boolean` | `date`), and `required`.
- String inputs that are identifiers must include a `validation` note.
- Do not add optional inputs unless the contract in `06-actions-and-power-automate-contracts.md` defines them.

## Step rules
- Steps must be numbered sequentially.
- Each step needs `action` and `detail`.
- Steps that call a connector must include `"connector": "Excel Online (Business)"`.
- Failure paths (INVALID_INPUT, NOT_VERIFIED, TEMPORARY_ERROR) must each have their own step.
- Never include a step that logs raw date of birth, phone digits, or full policy number.

## Output rules
- Output names must match the contract exactly — case-sensitive.
- Every flow must always return all declared outputs, including on failure (use blank strings, not null).
- `errorCode` must be one of: `NONE`, `INVALID_INPUT`, `NOT_VERIFIED`, `NOT_FOUND`, `DUPLICATE`, `TEMPORARY_ERROR`, `POLICY_BLOCK`.

## Test case rules
- Include one happy-path test case using the primary demo record: `POL-1001 / 1990-02-14 / 4821`.
- Expected output must show `verified: true` and `customerId: "CUS-1001"` for VerifyCustomer.
- Do not include a test case that uses real personal data.
