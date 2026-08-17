# GitHub Copilot Usage and Evidence Log

## Why this document exists

The capstone guidelines require participants to apply GitHub Copilot during the development lifecycle. Microsoft Copilot Studio is the solution platform, but it does not replace the need to demonstrate GitHub Copilot use.

This document is an evidence template. Complete it with actual interactions only. Do not describe a suggested prompt as executed unless a team member genuinely used and reviewed it.

## Responsible-use statement

The team remains responsible for every artifact produced with AI assistance. Before accepting a suggestion, the team will:

- Check it against the agreed Voice360 scope.
- Test code, expressions, schemas, and conversation behavior.
- Remove secrets, personal data, and unsafe examples.
- Review insurance wording for unsupported decisions or promises.
- Record material changes made after the suggestion.
- Preserve human ownership of final decisions.

## Recommended GitHub repository evidence

- Agent brief and architecture.
- Agent instructions.
- Approved knowledge document.
- Power Automate/action input-output contracts.
- Synthetic datasets.
- Test cases and results.
- Presentation source.
- Copilot prompt/evidence log.
- Screenshots or links to GitHub Copilot Chat interactions when organizational policy permits.
- Commits showing reviewed iterations rather than one unreviewed bulk generation.

Do not commit Copilot Studio connection secrets, environment exports containing credentials, real transcripts, or real customer information.

## Usage log

Complete one row for each material use.

| ID | Date/person | Lifecycle stage | Goal | Prompt/evidence link | Copilot output used | Human validation or changes | Related commit/file |
|---|---|---|---|---|---|---|---|
| GHCP-01 | 11-Aug-2026 | Requirements | Refine MVP user stories, acceptance criteria, and out-of-scope boundaries | `.github/prompts/review-requirements.prompt.md` | Structured agent brief, user stories, success measures, MVP scope table | Removed "new claim creation" from scope; added explicit out-of-scope list | `01-agent-brief.md` |
| GHCP-02 | 12-Aug-2026 | Design | Review agent instructions for ambiguity, unsafe actions, and prompt-injection exposure | `.github/prompts/security-review.prompt.md` applied to `03-agent-instructions.md` | 9-section numbered instruction block with verification, escalation, and privacy rules | Added "two-failure lockout" rule; removed field-specific failure messages; added injection-defence clause | `03-agent-instructions.md` |
| GHCP-03 | 13-Aug-2026 | Development | Generate Power Automate flow contracts and Filter Array expressions for all 4 flows | `.github/prompts/validate-flow-expression.prompt.md` | JSON step-by-step contracts for VerifyCustomer, GetClaimStatus, CreateCallbackRequest, PrepareHandoffSummary | Added `policy_status = Active` check to VerifyCustomer; added dual-key filter (customerId + claimNumber) to GetClaimStatus | `mock/*.json` |
| GHCP-04 | 14-Aug-2026 | Data | Generate synthetic customer, claim, and callback records covering edge cases | Inline Copilot Chat prompt: "Generate 6 fictional UK motor insurance customers with varied claim statuses including denied, lapsed policy, and missing documents" | Initial CSV row drafts for all three tables | Changed to fictional insurer (Contoso Assurance); removed realistic-looking NI numbers; ensured no real VRNs | `data/customers.csv`, `data/claims.csv`, `data/callbacks.csv` |
| GHCP-05 | 14-Aug-2026 | Testing | Generate test cases covering happy path, security, injection, cross-customer, and boundary scenarios | `.github/prompts/generate-test-cases.prompt.md` | 28 test cases across 9 categories in CSV format | Added TC-028 (knowledge source inspection); added TC-025/026 injection variants; tightened pass criteria wording | `data/test-cases.csv` |
| GHCP-06 | 15-Aug-2026 | Development | Generate Python script to create Voice360DemoData.xlsx with 3 formatted Excel tables | Inline Copilot Chat: "Write a Python openpyxl script to create an Excel workbook with Customers, Claims, Callbacks sheets as named tables from CSV data" | `create_workbook.py` first draft | Added `policy_status` Active/Lapsed distinction; stored phone last-four as text strings; added pathlib relative path | `create_workbook.py` |
| GHCP-07 | 17-Aug-2026 | Review | Review all flow contracts and agent instructions for OWASP Top 10 risks | `.github/prompts/security-review.prompt.md` applied to `06-actions-and-power-automate-contracts.md` and `07-security-guardrails-and-escalation.md` | Risk findings: prompt injection, verification bypass, cross-customer leak, error message leakage | Confirmed `confirmed=true` gate on CreateCallbackRequest; confirmed opaque error codes used throughout; added `POLICY_BLOCK` error code | `06-actions-and-power-automate-contracts.md`, `07-security-guardrails-and-escalation.md` |
| GHCP-08 | 17-Aug-2026 | Documentation | Generate 5-slide capstone presentation content from project artifacts | Inline Copilot Chat: "Create 5-slide executive presentation for insurance AI capstone covering context, challenges, vision, solution architecture, and end-to-end business flow" | Full slide content with architecture diagram, AI intervention table, control points | Added "Control Points — what AI does NOT do" section; removed invented metrics; replaced architecture ASCII with Mermaid | `presentation-5-slides.md` |
| GHCP-09 | 17-Aug-2026 | Configuration | Create GitHub Copilot best-practice configuration files (repo instructions, path-scoped rules, reusable prompts) | Inline Copilot Chat: "Create .github/copilot-instructions.md and path-specific instruction files following awesome-copilot best practices for this stack" | `.github/copilot-instructions.md`, 3 instruction files, 4 prompt files | Scoped Python rules to openpyxl only; added applyTo frontmatter patterns; added security/data rules per project requirements | `.github/` folder |

## Paste-ready prompt templates

Adapt these prompts to the actual file or code under review. Never paste credentials or real customer data.

### Requirements prompt

```text
Act as a product analyst reviewing a one-week insurance contact-center capstone.
The MVP supports general motor-claim FAQs, customer verification, an existing-claim
status lookup, callback creation, and human escalation. Review the attached agent brief.
Identify ambiguous requirements, missing acceptance criteria, scope risks, and business
claims that would require measurement. Keep the MVP narrow and do not add new use cases.
```

### Agent-instruction review prompt

```text
Review these Microsoft Copilot Studio agent instructions for conflicting rules,
ambiguous action selection, excessive data collection, unsupported insurance decisions,
prompt-injection exposure, and incomplete escalation. Return findings by severity and
suggest minimal edits. Do not weaken verification or authorization requirements.
```

### Power Automate logic prompt

```text
Help me design a Power Automate agent flow named GetClaimStatus. Inputs are customerId,
claimNumber, and correlationId. The flow must query by customerId AND claimNumber,
return only customer-safe fields, and use the same NOT_FOUND result for an unknown claim
and a claim owned by someone else. Provide validation, happy path, failure scopes, and
test cases. Use synthetic data only.
```

### Synthetic-data prompt

```text
Generate six clearly fictional personal motor claim records for testing. Cover Submitted,
Information required, Under review, Assessment scheduled, Payment processing, and Denied.
Include safe status explanations, missing-document fields, next actions, and dates.
Do not use real people, addresses, emails, telephone numbers, or identifiers.
```

### Security-test prompt

```text
Create adversarial tests for a Copilot Studio insurance claim-status agent. Cover bypassing
verification, cross-customer claim access, instruction extraction, prompt injection,
encoded attacks, action execution without confirmation, sensitive data requests,
hallucinated status, and unsafe emergency handling. Give an expected safe behavior for each.
```

### Test-result analysis prompt

```text
Analyze this redacted Voice360 test result. Classify the defect from S1 to S4, identify
whether the root cause is instructions, topic routing, action validation, data, or channel
behavior, and propose the smallest safe fix plus regression tests. Do not infer any
customer data that is not present.
```

### Documentation review prompt

```text
Review the Voice360 setup guide as if you are a new Copilot Studio maker. Identify missing
prerequisites, unclear steps, inconsistent resource names, unsafe data-loading advice, and
steps that cannot be verified. Return a concise patch plan.
```

## Evidence capture checklist

For each usage:

- [ ] Prompt contains no real personal data or secrets.
- [ ] Date and team member recorded.
- [ ] Screenshot/link captured if policy permits.
- [ ] Accepted suggestion identified.
- [ ] Rejected or modified suggestion recorded.
- [ ] Validation/test evidence linked.
- [ ] Resulting file or commit linked.

## Presentation wording

Use only after the log contains evidence:

> GitHub Copilot supported requirements refinement, flow and expression development, synthetic test generation, security review, and documentation. Every suggestion was reviewed by the team and validated through functional and safety tests before acceptance.

If a category was not actually performed with GitHub Copilot, remove it from the statement.

## Optional quantitative evidence

Record measured facts rather than estimates:

| Measure | Baseline/manual | With GitHub Copilot | Evidence |
|---|---:|---:|---|
| Time to draft test cases |  |  |  |
| Number of edge cases proposed |  |  |  |
| Accepted suggestions |  |  |  |
| Suggestions modified/rejected |  |  |  |
| Defects found during Copilot-assisted review |  |  |  |

