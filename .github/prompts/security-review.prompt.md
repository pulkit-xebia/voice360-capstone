---
mode: ask
---

Act as an OWASP-aware security reviewer for a Microsoft Copilot Studio agent called Voice360.

The agent handles motor insurance claim enquiries for fictional customers. It connects to Power Automate flows that read from an Excel workbook. No real customer data is used.

Review the attached file (agent instructions, flow contract, or topic design) and check for:

1. **Prompt injection** — can a user input override the agent's instructions or change its role?
2. **Verification bypass** — is there any path that reveals claim data without VerifyCustomer returning verified=true?
3. **Cross-customer data leak** — could one customer retrieve another customer's claim?
4. **Sensitive data exposure** — does any output return a full phone number, full DOB, full policy number, or internal system detail?
5. **Insecure write operations** — can CreateCallbackRequest be called without confirmed=true?
6. **Error message leakage** — do failure paths return connector errors, table names, or stack traces?
7. **Scope creep via conversation** — can the user convince the agent to make a coverage decision, update a claim, or process a payment?

For each finding: state the risk, the location in the file, the severity (High/Medium/Low), and a suggested fix.
Output as a numbered list.
