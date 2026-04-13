# Project 1: IT Helpdesk Triage Support Tool

A generative AI-powered productivity app built on **AWS PartyRock** (Amazon Bedrock Playground) that automates first-line IT helpdesk triage. The app takes a plain-language issue description and technology area, then generates a structured support ticket and a step-by-step troubleshooting guide.

🔗 **Live App:** [IT Helpdesk Triage Support Tool on PartyRock](https://partyrock.aws/u/Jesse-9/__i8Q8kvk/IT-Helpdesk-Triage-Support-Tool)

---

## The Problem

First-line IT support analysts, especially in small teams, face a recurring challenge: end users describe problems vaguely, and the analyst must simultaneously classify the issue, determine priority, generate troubleshooting steps, and decide whether to escalate. This process is inconsistent when done from memory under pressure, and the quality of triage varies depending on who handles the call.

## The Solution

This app standardises the entire triage workflow. Any analyst using the tool gets the same structured, professional output regardless of their experience level. It encodes prompt engineering into a reusable, shareable tool that produces consistent results every time.

---

## App Architecture

### Inputs

| Input | Type | Purpose |
|-------|------|---------|
| **Issue Description** | Free text | The IT issue exactly as reported by the end user |
| **Technology Area** | Dropdown | Categorises the affected system to guide the AI response |

**Technology Area options:**
- Email & Collaboration (Outlook, Teams, SharePoint)
- Identity & Access (Active Directory, Entra ID, Passwords)
- Network & Connectivity
- Endpoint / Hardware (Laptop, Desktop, Printer)
- Windows OS
- Cloud Services (Microsoft 365, Azure)
- Phone System
- Database
- Other

### Outputs

| Output | Type | What It Generates |
|--------|------|-------------------|
| **Support Ticket** | AI Text Generation | Structured ticket with category, subcategory, priority (P1-P4) with justification, impact assessment, initial diagnosis, estimated resolution time, and additional notes |
| **Troubleshooting Guide** | AI Text Generation | Diagnostic questions to ask, numbered step-by-step troubleshooting, relevant commands with expected outputs, workarounds, and escalation criteria with evidence checklist |

Both outputs are powered by **Claude Sonnet** via Amazon Bedrock.

---

## Prompt Engineering

### Support Ticket Prompt

The prompt assigns the role of an experienced IT helpdesk coordinator and enforces a strict output format with six labelled sections. It references both user inputs dynamically.

**Key design decisions:**
- Structured output format ensures tickets are consistent and ready for logging in any ITSM tool
- Priority justification is required, not just a label, so the analyst understands the reasoning
- Impact assessment includes business impact, not just user count

**Responsible AI constraint added:**
> *Do not fabricate technical details that are not present or clearly implied in the issue description. If the description is vague or missing key information, explicitly note what the analyst should confirm with the end user before proceeding. Never assume severity without evidence.*

### Troubleshooting Guide Prompt

The prompt assigns the role of a senior IT support engineer writing a live guide for an analyst currently on a call. This framing ensures the output is practical and real-time usable, not theoretical.

**Key design decisions:**
- Steps are ordered from simplest to most complex, matching real helpdesk methodology
- Commands include the exact syntax and what to look for in the output
- Escalation criteria are specific conditions, not vague suggestions

**Responsible AI constraint added:**
> *Only recommend steps that are relevant to the reported symptoms. Do not suggest destructive actions (e.g., factory reset, data deletion) without clearly flagging the risk. If the issue description is too vague to troubleshoot confidently, state what additional information is needed first.*

---

## Testing

The app was tested across five diverse scenarios to verify it adapts properly to different issue types:

| Test | Issue | Technology Area | Expected Priority | Result |
|------|-------|----------------|-------------------|--------|
| 1 | User cannot open Outlook, shows disconnected, Teams won't load, others unaffected | Email & Collaboration | P3 Medium | ✅ Correctly classified as P3, single user impact |
| 2 | Entire office lost internet 20 minutes ago, all devices including Wi-Fi phones affected | Network & Connectivity | P1 Critical | ✅ Correctly classified as P1, organisation-wide impact |
| 3 | 403 forbidden error accessing HR folder on SharePoint, other sites work fine | Email & Collaboration | P3 Medium | ✅ Correctly classified as P3, single user, permissions-based diagnosis |
| 4 | Laptop extremely slow since Windows update last night, freezing intermittently | Windows OS | P3 Medium | ✅ Correctly classified as P3, single user, update-related diagnosis |
| 5 | External emails bouncing back, internal emails working, clients complaining | Email & Collaboration | P2 High | ✅ Correctly classified as P2, organisation-wide external communication impact |

**Key finding:** The app does not produce generic responses. The entire output structure, including commands, escalation paths, and workarounds, adapts completely based on the scenario.
---

## App vs Chatbot Comparison

The same issue was submitted to both the PartyRock app and a regular chatbot to compare outputs:

| Aspect | Regular Chatbot | PartyRock App |
|--------|----------------|---------------|
| **Format** | Two loose paragraphs | Structured sections with clear headers |
| **Ticket classification** | None | Full classification with priority justification |
| **Troubleshooting structure** | Mixed together in flowing text | Numbered steps, simplest to most complex |
| **Diagnostic commands** | Brief mention of clearing cache | Full table with commands and expected outputs |
| **Escalation guidance** | None | Specific criteria with evidence checklist |
| **Workarounds** | None | Multiple options listed |
| **Consistency** | Different format every time | Same structured format every run |
| **Shareability** | Would need to copy-paste conversation | Anyone with the link gets the same tool |

**Conclusion:** The chatbot gives a conversation. The app gives a repeatable, shareable workflow. This is the core value of building an AI application rather than relying on ad-hoc prompting.

---

## Responsible AI Considerations

- **Anti-hallucination:** Both prompts explicitly instruct the model not to fabricate details and to flag when information is missing or vague
- **Safety:** The troubleshooting guide prompt prevents suggesting destructive actions without flagging risk
- **Transparency:** The support ticket output notes when assumptions are being made and what needs to be confirmed
- **Escalation over overconfidence:** The app recommends escalation rather than over-promising resolution when the issue is beyond first-line scope

---

## Real-World Applicability

This app mirrors the functionality of production tools like:
- **ServiceNow Virtual Agent** — automated ticket classification and routing
- **Microsoft Copilot for Service** — AI-assisted troubleshooting for support agents

In a production environment, this pattern could be extended using **Amazon Bedrock** with:
- **RAG (Retrieval-Augmented Generation)** to pull from an organisation's internal knowledge base and past ticket history
- **API integration** with an ITSM platform like ServiceNow or Jira Service Management for automatic ticket creation
- **Fine-tuning** on organisation-specific terminology and resolution patterns

---

## Why I Built This

I volunteer as IT support for a small business called Guest Turnover Solutions, managing their live Microsoft 365 environment. I regularly take vague issue descriptions from non-technical users and need to triage them quickly and consistently. This app solves that problem by encoding my triage process into a reusable tool that any analyst could use.

I'm working towards my first IT support role or Level 3 apprenticeship, and I wanted this project to demonstrate that I don't just study IT concepts — I build practical tools around them.

---

## Built With

- **[AWS PartyRock](https://partyrock.aws)** — Amazon Bedrock Playground
- **Amazon Bedrock** — Underlying infrastructure for model access

---

## Course

**AWS AI Practitioner Challenge** — Part of the AWS Agentic Engineer Nanodegree through Udacity
