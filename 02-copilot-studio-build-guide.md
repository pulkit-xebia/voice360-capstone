# Microsoft Copilot Studio Build Guide

This guide is a build checklist for the capstone, not a production deployment guide. Microsoft changes the authoring interface periodically, so labels may differ slightly by environment.

## 1. Confirm prerequisites

Before building, record the result of each check.

| Requirement | Required for chat MVP | Required for telephony | Status |
|---|---:|---:|---|
| Copilot Studio maker access | Yes | Yes | To confirm |
| Dataverse environment | Preferred | Preferred | To confirm |
| Power Automate/agent flows | Yes | Yes | To confirm |
| Permission to publish to Teams or demo website | Yes | No | To confirm |
| Dynamics 365 Contact Center voice integration | No | Yes | To confirm |
| Azure Communication Services phone number | No | Yes | To confirm |
| Live-agent engagement hub | No; simulated handoff is acceptable | Needed for real transfer | To confirm |

Decision date: **13 August 2026**. If voice prerequisites are not available, proceed immediately with the complete chat journey and treat phone integration as the next phase.

## 2. Create the agent

1. Open Microsoft Copilot Studio in the approved environment.
2. Create a new agent.
3. Use the values under **Agent setup values** in `03-agent-instructions.md`.
4. Select English as the primary language.
5. Enable generative orchestration if available in the environment.
6. Keep general model knowledge disabled for the demonstration if the setting is available. The agent should answer from approved knowledge or explicitly say that it cannot confirm.
7. Save the agent before adding resources.

## 3. Add approved knowledge

1. Open the agent's Knowledge page.
2. Add `04-insurance-knowledge-base.md` as an uploaded-file knowledge source.
3. Use this description:

   > Approved general information for Contoso Assurance personal motor claims, including claim stages, document requirements, timelines, communications, privacy, and escalation. It contains no customer-specific claim records and must not be used to retrieve an individual claim status.

4. Wait for the source status to become **Ready**.
5. Ask three FAQ test questions before proceeding.

Do **not** upload the CSV customer, claim, or callback files as agent knowledge. Copilot Studio uploaded knowledge can be available to all users of the agent. Customer-specific information must be returned by an action only after verification.

## 4. Create the mock data store

### Preferred: Dataverse

Create these tables and import the matching CSV files:

- `Voice360 Customers` from `data/customers.csv`
- `Voice360 Claims` from `data/claims.csv`
- `Voice360 Callbacks` from `data/callbacks.csv`

Use `customer_id`, `claim_number`, and `callback_reference` as alternate keys if the environment permits.

### Fallback: Excel in OneDrive

1. Create one workbook named `Voice360DemoData.xlsx`.
2. Create formatted tables named `Customers`, `Claims`, and `Callbacks`.
3. Import the matching CSV into each table.
4. Store the workbook in a team-controlled OneDrive or SharePoint location.
5. Do not expose the workbook as a generative knowledge source.

Excel is suitable for a low-concurrency demo only. Dataverse is the recommended option for reliable actions.

## 5. Create actions

Build these agent flows using `06-actions-and-power-automate-contracts.md`:

1. `VerifyCustomer`
2. `GetClaimStatus`
3. `CreateCallbackRequest`
4. `PrepareHandoffSummary`

For every action:

- Give it the exact name above.
- Copy the action description from the contract.
- Define typed inputs and outputs.
- Validate all inputs.
- Return structured failures instead of raw connector errors.
- Use a connection owned by the approved demo/service account.
- Turn off verbose connector output that might reveal data.
- Test the action independently before connecting it to a topic.

## 6. Create topics

Create or configure the topics described in `05-conversation-and-topic-design.md`:

- `Check Claim Status`
- `Claim Document Guidance`
- `Request Callback`
- `Human Escalation`
- `Verification Failure`
- System topics: Greeting, Conversation Start, Fallback, Escalate, End of Conversation

Use deterministic topic/action logic for verification, claim lookup, callback creation, and escalation. Use generative answers only for general informational questions.

## 7. Add instructions

1. Open the Overview page.
2. Edit the Instructions section.
3. Paste the content under **Paste-ready instructions** from `03-agent-instructions.md`.
4. After all actions and topics exist, replace names in backticks with slash-selected Copilot Studio resources where supported. Exact references help the orchestrator select the intended resource.
5. Save and inspect the activity map while testing.

## 8. Configure authentication and verification

The capstone uses explicit mock verification to demonstrate the flow. It is not production-grade identity proofing.

- Do not ask for a full government ID, payment card, password, or one-time password.
- Ask for policy number, date of birth, and the last four phone digits.
- Set `Global.IsVerified` only from a successful `VerifyCustomer` result.
- Store only the returned `customerId`, `customerFirstName`, `correlationId`, and verification time.
- Clear verification variables on end, timeout, escalation completion, or restart.

For production, replace mock verification with approved OAuth/Entra/customer-identity controls and server-side authorization.

## 9. Configure escalation

### With an engagement hub

Connect the supported engagement hub and use the built-in Escalate system topic. Pass the conversation history and handoff variables.

### Capstone fallback

If live-agent integration is unavailable:

1. Call `PrepareHandoffSummary`.
2. Display the summary in the test pane.
3. Create a callback request if the customer agrees.
4. Explain during the demo that a production engagement hub would route the same summary to a live agent.

## 10. Test and publish

1. Run every critical test from `data/test-cases.csv`.
2. Record actual results and evidence links.
3. Fix every severity-1 or severity-2 defect.
4. Confirm no real personal data appears in transcripts, actions, or files.
5. Publish to the approved test channel.
6. Re-run the demo journey in the published channel.
7. Capture screenshots for the presentation.

## 11. Minimum screenshots

- Agent Overview showing name and description.
- Agent instructions.
- Approved knowledge source in Ready state.
- Topics/actions list.
- Successful `CLM-1042` conversation.
- Callback reference or handoff summary.
- Test result/activity map.
- GitHub repository showing documentation and Copilot evidence.

## 12. Final handoff checklist

- [ ] Agent name and description configured.
- [ ] Paste-ready instructions added.
- [ ] Approved knowledge uploaded and Ready.
- [ ] Mock data imported into Dataverse or Excel.
- [ ] Four actions created and tested.
- [ ] Required topics created.
- [ ] Guardrails and escalation rules verified.
- [ ] Critical tests passed.
- [ ] Published demo works.
- [ ] Screenshots captured.
- [ ] GitHub Copilot evidence log completed.
- [ ] Solution exported if environment permissions allow.

