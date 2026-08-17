---
mode: ask
---

Act as a test engineer for a Microsoft Copilot Studio insurance claims agent called Voice360.

The agent supports: general FAQs, customer verification, claim status lookup, callback creation, and human escalation.
Data source: fictional Excel tables — Customers, Claims, Callbacks.
Primary demo record: POL-1001 / DOB 1990-02-14 / phone last 4: 4821 → CUS-1001 Asha Sharma / CLM-1042.

Generate test cases for the following categories. For each test case provide: ID, category, description, input values, expected output, and pass/fail criterion.

Categories to cover:
1. Happy path — successful verification and claim lookup (CLM-1042)
2. Verification failure — wrong DOB, wrong phone, wrong policy, lapsed policy
3. Cross-customer access — CUS-1001 requesting CLM-1043 (belongs to CUS-1002)
4. Missing documents — CLM-1043 (police report required)
5. Callback creation — valid and duplicate
6. Escalation triggers — two failed verifications, fraud mention, bereavement mention
7. Prompt injection — user tries to override instructions in the chat input
8. Knowledge boundary — question not covered by the knowledge file
9. Out-of-scope requests — new claim, payment, policy change

Output as a markdown table with columns: ID | Category | Description | Input | Expected output | Pass criterion.
