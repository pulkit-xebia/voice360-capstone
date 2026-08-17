# Security Policy — Voice360

## Scope

Voice360 is a capstone proof of concept using fictional data only. It is not a production system and does not process real customer information.

## What to report

If you find a pattern in this repository that could enable:

- Real PII exposure (real names, real policy numbers, real dates of birth)
- Credential or connection secret leakage
- Prompt injection bypass of the agent's security instructions
- Verification bypass (claim data accessible without `VerifyCustomer` returning `verified=true`)
- Cross-customer data access (one customer's claim visible to another)

Please open a GitHub Issue with the label `security` and describe the location and potential impact. Do not include sensitive values in the issue.

## Enforced guardrails

| Control | Where enforced |
|---|---|
| No real customer data | `.gitignore`, `data/` contains synthetic records only |
| No credentials committed | `.gitignore` + `.github/hooks/pre-commit` |
| Opaque error codes | `06-actions-and-power-automate-contracts.md`, all mock flows |
| Verification required before claim data | `03-agent-instructions.md` rule 4, all flow contracts |
| Claim ownership check | `GetClaimStatus` filters by `customerId` AND `claimNumber` |
| Confirmation before write | `CreateCallbackRequest` requires `confirmed=true` |
| Prompt injection defence | `03-agent-instructions.md` rule 8 |
| Knowledge source isolation | `04-insurance-knowledge-base.md` contains no customer records |

## Security review prompt

Use `.github/prompts/security-review.prompt.md` with GitHub Copilot to run a structured OWASP-aligned review of any file in this repository.

## Out of scope

- Denial-of-service testing
- Infrastructure or network scanning
- Any testing against real Microsoft tenants or production systems
