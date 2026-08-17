# Voice360 Agent Brief

## Executive summary

Insurance contact centers face rising service demand, cost pressure, talent constraints, and high employee turnover. Customers still expect immediate, natural support, but often encounter long queues and rigid IVR menus. Voice360 demonstrates how a conversational AI agent can answer routine questions, retrieve claim information, and transfer complex cases to a human with full context.

For this capstone, Voice360 is a functional proof of concept for **personal motor claim servicing** at the fictional company **Contoso Assurance**. The solution is intentionally narrow enough to build and validate within one week.

## Problem statement

Policyholders frequently contact insurers to ask:

- What is the status of my claim?
- Are any documents missing?
- What happens next?
- When will I receive another update?
- Can I speak to a person?

Agents repeatedly retrieve the same information, explain standard claim stages, and document calls. Customers wait while agents spend time on low-complexity servicing instead of exceptions that require judgment.

## Objective

Create a Microsoft Copilot Studio agent that provides immediate, grounded claim assistance and completes safe servicing actions using mock data.

## Target users

- Primary: Personal motor policyholders with an existing claim.
- Secondary: Contact-center agents receiving an escalated conversation.
- Demo users: Capstone reviewers and project team members using fictional records.

## MVP scope

### In scope

- English-language chat demonstration.
- Voice channel if the required tenant, Contact Center, and phone-number capabilities are available.
- General questions about the claim process and required documents.
- Customer verification using policy number, date of birth, and phone last four digits.
- Claim-status lookup from fictional data.
- Explanation of missing documents and next action.
- Callback creation.
- Explicit and policy-driven escalation.
- A structured handoff summary.

### Out of scope

- New-policy quotations or purchases.
- First notice of loss or creation of a new claim.
- Claim approval, denial, or coverage decisions.
- Payments, bank-detail changes, or refunds.
- Modification or deletion of claim records.
- Legal, medical, financial, or repair advice.
- Real customer data or production integrations.
- Multilingual support for the capstone MVP.

## Primary user story

As a policyholder, I want to ask for my claim status in natural language, verify my identity, understand the latest status and next step, and request a human callback if I still need help.

## Supporting user stories

1. As a policyholder, I want to know which documents are usually required for a motor claim.
2. As a policyholder, I want the agent to tell me when a required document is missing.
3. As a policyholder, I want to ask for a human at any point.
4. As a contact-center agent, I want an escalation summary so I do not ask the customer to repeat the conversation.
5. As a compliance owner, I want the agent to avoid unsupported decisions and protect customer information.

## Success measures

| Measure | Capstone target |
|---|---:|
| Supported-intent completion | At least 90% of scripted happy-path tests |
| Grounded FAQ accuracy | 100% for approved test questions |
| Unauthorized claim disclosure | 0 occurrences |
| Successful claim lookup after verification | Under 30 seconds in the demo |
| Handoff summary completeness | All required fields included |
| Critical test pass rate | 100% |

These are prototype validation targets, not production performance claims.

## Acceptance criteria

### General knowledge

- The agent answers from the approved knowledge source.
- The response distinguishes general guidance from customer-specific information.
- If the answer is absent, the agent says it cannot confirm and offers escalation.

### Verification

- All three verification values must match one fictional customer record.
- Failure gives a generic response and reveals no matching-field detail.
- Two failed attempts cause the agent to stop verification and offer human help.
- Verification state is cleared when the conversation ends.

### Claim status

- The customer must be verified before a claim lookup.
- The action confirms that the claim belongs to the verified customer.
- The response includes claim number, status, latest update, missing documents, next step, and expected update date when available.
- The agent never predicts approval or payment beyond returned data.

### Callback and escalation

- The agent confirms the reason and preferred time before creating a callback.
- A successful callback returns a reference number.
- An explicit request for a person is honored.
- The handoff includes intent, verification result, claim number, latest status, actions taken, and unresolved question.

## Assumptions and dependencies

- Rohit has maker access to a Copilot Studio environment.
- Power Automate or agent flows are enabled.
- Dataverse is preferred; a formatted Excel workbook stored in OneDrive is an acceptable capstone fallback.
- The team uses only synthetic records supplied in this pack.
- Real-time voice needs the appropriate Microsoft/Dynamics 365 Contact Center setup and an Azure Communication Services phone number.
- If voice prerequisites are unavailable, the evaluated prototype will use chat, with voice shown in the target architecture and roadmap.

## Expected business value

- Reduce routine claim-status calls handled by people.
- Provide consistent, always-available answers.
- Shorten customer wait time.
- Reduce repeated questioning during escalation.
- Allow human agents to focus on sensitive and complex exceptions.
- Create an auditable basis for safe conversational automation.

