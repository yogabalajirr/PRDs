# TicketFlow — Product Requirements Document

> **Lightweight customer support ticketing for small teams** — a faster, simpler alternative to Zendesk and Freshdesk for businesses with 5–50 agents.

| Field | Value |
|---|---|
| **Document** | Product Requirements Document (PRD) |
| **Product** | TicketFlow — Customer Support Platform |
| **Version** | 1.0 |
| **Status** | MVP — Built & validated |
| **Author** | Yogabalaji R R |
| **Last updated** | June 2026 |

---

## Contents

1. [Overview](#1-overview)
2. [Problem statement & background](#2-problem-statement--background)
3. [Market & competitive landscape](#3-market--competitive-landscape)
4. [Goals, non-goals & success metrics](#4-goals-non-goals--success-metrics)
5. [Target users & personas](#5-target-users--personas)
6. [User stories](#6-user-stories)
7. [Requirements](#7-requirements)
8. [AI assist layer](#8-ai-assist-layer)
9. [Non-functional requirements](#9-non-functional-requirements)
10. [Release plan & phasing](#10-release-plan--phasing)
11. [Risks, assumptions & dependencies](#11-risks-assumptions--dependencies)
12. [Open questions](#12-open-questions)
- [Appendix A — Out of scope for v1](#appendix-a--out-of-scope-for-v1-v2-candidates)
- [Appendix B — Data model](#appendix-b--data-model-entities)

---

## 1. Overview

TicketFlow is a customer support ticketing platform built for small and mid-sized teams — the businesses running support with **5 to 50 agents** who find Zendesk and Freshdesk too expensive, too complex, and too slow to set up. The product does the core support job well rather than chasing feature breadth.

The MVP delivers the full ticketing loop — capture, triage, respond, resolve — across email, an embeddable website widget, and manual entry, with SLA tracking, automated routing, a no-login customer portal, reporting, and an AI assist layer. The guiding constraint is a **sub-15-minute setup**: a new team should go from signup to handling its first real ticket in under fifteen minutes.

### TL;DR

- **Who it's for:** SMB support teams (5–50 agents) underserved by heavyweight incumbents.
- **Core promise:** Handle real tickets within 15 minutes of signup, without an implementation project.
- **What ships in v1:** The complete ticketing loop plus SLA, routing, customer portal, reporting, and AI assist (13 capabilities).
- **What's deliberately deferred:** Live chat, knowledge base, extra channels, mobile apps, public API, and billing UI — reserved for v2.

---

## 2. Problem statement & background

Small support teams are stuck between two bad options. Shared email inboxes (a single Gmail account everyone logs into) are free and instant but fall apart past a handful of agents — tickets get answered twice or not at all, there is no ownership, no SLA visibility, and no reporting. The dedicated tools that solve this (Zendesk, Freshdesk) are built for large organisations: they are priced per-agent at a premium, carry hundreds of features a small team never touches, and require days of configuration before the first ticket is handled.

The cost of leaving this unsolved is concrete: small teams either **overpay for complexity they don't use**, or stay on a shared inbox and **drop customer requests** — missing response-time expectations, losing context across replies, and damaging trust with the very customers a small business can least afford to lose.

### Why now

The tooling to build a lean alternative has matured: managed email infrastructure with reliable inbound/outbound threading, multi-tenant patterns that are well understood, and accessible AI APIs that can offload triage and drafting. A small team can now ship a focused product that is genuinely faster to adopt than the incumbents — turning "simple and quick" into a defensible wedge rather than a limitation.

---

## 3. Market & competitive landscape

TicketFlow competes in the help-desk / customer-support software category, but deliberately targets the under-served low end rather than competing head-on at the enterprise tier.

| Player | Strength | Gap TicketFlow exploits |
|---|---|---|
| **Zendesk** | Deep, mature, enterprise-grade; vast integration ecosystem. | Expensive and complex; long setup; overkill for small teams. |
| **Freshdesk** | Broad feature set; established brand; tiered pricing. | Feature bloat and configuration overhead for a 5–50 agent team. |
| **Shared inbox (Gmail / Outlook)** | Free, familiar, zero setup. | No ownership, SLA, routing, or reporting; breaks down past a few agents. |
| **TicketFlow** | Core loop done well; sub-15-min setup; lower price. | — (positioning: speed + simplicity + price) |



---

## 4. Goals, non-goals & success metrics

### 4.1 Goals

Goals are framed as outcomes, not features.

- **Time to first value:** A new workspace reaches its first handled ticket in under 15 minutes from signup.
- **Reliable core loop:** Teams of 5–50 agents can capture, triage, respond to, and resolve tickets without items slipping through the cracks.
- **Match the daily job at a lower bar:** Cover the support workflows SMBs actually perform daily, at lower price and complexity than incumbents.
- **Reduce handling time:** Cut the effort per ticket through AI-assisted triage/drafting and canned responses.
- **Protect responsiveness:** Give small teams live SLA visibility so commitments are met without a dedicated ops person watching the queue.

### 4.2 Non-goals (v1)

Explicit non-goals keep the MVP tight and prevent scope creep.

- **Not building live chat.** Real-time chat needs separate infrastructure and a different support model; deferred to v2.
- **Not building a knowledge base.** A self-serve help center is a large content surface that does not serve the core ticketing loop yet.
- **Not adding phone / WhatsApp / Slack channels.** Email + widget + manual entry covers the dominant SMB intake paths; channel breadth is a v2 expansion.
- **Not shipping native mobile apps.** A responsive web app is sufficient for agents at launch.
- **Not building a billing / subscriptions UI.** Monetisation is deferred until product-market fit signals are clear.
- **Not exposing a public API or marketplace.** An integration ecosystem is premature before the core product is validated.

### 4.3 Success metrics

> **Note:** targets below are pre-launch hypotheses for an MVP with no historical baseline. They define what "good" looks like and should be revised once real usage data exists.

**Leading indicators (days–weeks)**

| Metric | Target | Stretch | How measured |
|---|---|---|---|
| Setup completion — % of new workspaces that handle a first ticket within 15 min of signup | 60% | 75% | Onboarding funnel event: signup → first ticket reply |
| Activation — % of workspaces resolving ≥5 tickets in first 7 days | 40% | 55% | Ticket status transitions per workspace |
| AI suggestion acceptance — % of AI drafts sent with minor edits | 30% | 45% | Draft generated vs. sent, with edit-distance threshold |
| SLA breach rate per workspace | <10% | <5% | SLA timer outcomes from background workers |

**Lagging indicators (weeks–months)**

| Metric | Target | Stretch | How measured |
|---|---|---|---|
| 30-day workspace retention | 50% | 65% | Active workspaces still handling tickets at day 30 |
| Average first-response time across workspaces | <4 bh | <2 bh | Time from ticket creation to first agent reply (business hours) |
| Average resolution time | <2 days | <1 day | Time from creation to Resolved |
| Trial → paid conversion (post-billing) | TBD | TBD | Deferred until billing UI ships in v2 |

---

## 5. Target users & personas

| Persona | Role | Primary goals | Pain points today |
|---|---|---|---|
| **Support Lead** | Workspace Owner / Admin | Stand up the team fast; configure SLA and routing; keep the queue under control; report to leadership. | Setup of incumbent tools takes days; bloated config; hard to prove team performance. |
| **Support Agent** | Agent | Triage and answer tickets quickly; avoid duplicate work; keep context in one place. | Shared inbox chaos; no ownership; constant context-switching; repetitive replies. |
| **End Customer** | External customer | Get help and see updates with minimal effort. | Forced account creation; broken email threads; no visibility into status. |
| **Manager / Viewer** | Viewer | See volumes, response times, and SLA health without touching tickets. | No lightweight read-only reporting in current setup. |

---

## 6. User stories

**Workspace Owner / Admin**

- As an admin, I want to create a workspace and invite my agents by email so that the team can start working within minutes.
- As an admin, I want to configure SLA policies per priority with my business hours so that response commitments are tracked automatically.
- As an admin, I want auto-assignment rules so that incoming tickets land with the right agent or team without manual triage.
- As an admin, I want a reporting dashboard so that I can see volume, response times, and SLA compliance at a glance.

**Agent**

- As an agent, I want a single inbox with filters (My Open, Unassigned, Overdue) so that I always know what to work on next.
- As an agent, I want to reply to a customer and have it thread correctly over email so that the conversation stays coherent.
- As an agent, I want to leave an internal note and @mention a teammate so that I can ask for help without the customer seeing it.
- As an agent, I want canned responses with variables so that I can answer common questions quickly and consistently.
- As an agent, I want an AI-suggested draft reply so that I can respond faster while staying in control of the final message.

**End customer**

- As a customer, I want to reply to a support email and have it reach the right ticket so that I don't have to repeat myself.
- As a customer, I want to view my ticket and add replies via a link without creating an account so that getting help is effortless.

**Edge & boundary cases**

- As a customer, when I reply to a resolved ticket, I want it to reopen so that my follow-up isn't lost.
- As an agent, when the background worker is unavailable, I still want SLA state to display so that the inbox degrades gracefully rather than breaking.
- As an admin, when a routing rule matches no agent, I want the ticket to land in Unassigned so that it is never silently dropped.

---

## 7. Requirements

Requirements are prioritised with MoSCoW logic. The P0 test: *"if we cut this, does the product still solve the core problem of handling support tickets for a team?"* If no, it is P0.

### 7.1 Priority summary

| # | Capability | Priority | Rationale |
|---|---|---|---|
| 1 | Multi-tenant workspaces & authentication | **P0** | Nothing works without isolated, secure tenancy. |
| 2 | Multi-channel capture (email, widget, manual) | **P0** | Tickets must be able to enter the system. |
| 3 | Unified agent inbox with filters | **P0** | The queue is where agents live. |
| 4 | Ticket lifecycle, status & activity log | **P0** | Core state machine of a support tool. |
| 5 | Agent reply with email threading | **P0** | Responding is half the loop. |
| 6 | Internal notes | **P0** | Team collaboration is core to a multi-agent tool. |
| 7 | No-login customer portal | **P0** | Low-friction customer access is a key promise. |
| 8 | Roles & permissions | **P0** | Owner/Admin/Agent/Viewer separation is required for teams. |
| 9 | Auto-assignment rules (round-robin + builder) | P1 | Big efficiency gain; loop works manually without it. |
| 10 | SLA tracking, timers & alerts | P1 | Key differentiator; not required to handle a ticket. |
| 11 | Canned responses (macros) | P1 | Speeds replies; not essential to send one. |
| 12 | @mentions & notifications | P1 | Enhances collaboration on top of notes. |
| 13 | Reporting dashboard + CSV export | P1 | Drives retention; not needed to operate day one. |
| 14 | AI layer (auto-tag, reply suggest, summary) | P1 | Differentiator; full human override required. |
| 15 | Desktop app (Electron, macOS) | P1 | Convenience wrapper; web app is the source of truth. |
| 16 | v2 expansion set (see Appendix A) | P2 | Explicitly out of scope for v1. |

### 7.2 Must-have (P0)

**P0-1 · Multi-tenant workspaces & authentication**
Every signup creates an isolated workspace with its own subdomain and branding (logo, brand colour). Email + password authentication with JWT sessions. Tenant isolation is enforced at the query layer — every database query is scoped by workspace.
- *Acceptance:*
  - Given a signed-out request, when it hits any API route, then it receives HTTP 401.
  - Given two workspaces, when one queries tickets, then it can never read another workspace's data.
  - API responses for users never include password hashes (explicit field selection).

**P0-2 · Multi-channel capture**
Tickets enter three ways: inbound email (parsed for sender, subject, body, attachments, and threading headers), an embeddable JavaScript widget added with a single script tag, and manual creation by an agent.
- *Acceptance:*
  - Given an inbound email, when it is received, then a ticket is created with sender, subject, body, and attachments preserved.
  - Given the widget script on any site, when a visitor submits the form, then a ticket appears in the inbox.
  - An agent can create a ticket manually from the dashboard.

**P0-3 · Unified agent inbox**
A single queue with default filters (My Open, Unassigned, Overdue, All Open, Closed) and custom filters by status, priority, assignee, tag, channel, and date range. Supports bulk actions and saveable views.
- *Acceptance:*
  - Default and custom filters return the correct ticket set.
  - Bulk actions apply to all selected tickets and are written to the activity log.
  - A saved view persists and is retrievable by the agent.

**P0-4 · Ticket lifecycle, status & activity log**
Tickets move Open → Pending → On Hold → Resolved → Closed. Tickets carry a priority (Low / Normal / High / Urgent) and free-form tags. A customer reply to a resolved ticket reopens it. Every state change is written to an immutable activity log.
- *Acceptance:*
  - Each status transition is recorded immutably with actor and timestamp.
  - Given a Resolved ticket, when the customer replies, then the ticket reopens to Open.

**P0-5 · Agent reply with email threading**
Agents reply from the ticket view; outbound email carries proper threading headers (message-ID / references) so customer mail clients keep the conversation in one thread.

**P0-6 · Internal notes**
Agents add notes visible only to the team, shown with a visually distinct treatment so they are never confused with customer-facing replies.

**P0-7 · No-login customer portal**
Customers receive a signed-token URL to view their ticket thread, add replies, and attach files — no account required. Optional account creation lets a customer see all their tickets.
- *Acceptance:*
  - A valid signed token grants access to exactly one ticket thread and nothing else.
  - An expired or tampered token is rejected.

**P0-8 · Roles & permissions**
Roles are Owner, Admin, Agent, and Viewer. Teams can be created within a workspace. Invites are sent via magic-link email.

### 7.3 Nice-to-have (P1)

- **Auto-assignment rules.** Round-robin load balancing across a team, plus a rule builder for up to 20 active routing rules evaluated top-down (e.g. "IF sender domain = acme.com THEN assign to Enterprise Team AND set priority High").
- **SLA tracking & alerts.** SLA policies configurable per priority and per workspace with timezone-aware business hours. Each ticket shows a live countdown with three states — green (on time), amber (75% elapsed), red (breached). Email and in-app alerts fire at 75% and on breach via background workers.
- **Canned responses (macros).** Workspace-wide and personal canned responses with variable substitution (`{{customer_name}}`, `{{ticket_id}}`, `{{agent_name}}`, `{{company_name}}`), triggered via a `/` slash command in the reply editor.
- **@mentions & notifications.** @mentioning an agent in a note triggers both in-app and email notification.
- **Reporting dashboard + CSV export.** Last 7/30 days: tickets created, tickets resolved, average first-response time, average resolution time, SLA compliance %. Agent leaderboard and a daily volume chart by day, channel, and tag. All data CSV-exportable.
- **Desktop app.** An Electron wrapper packages the web app as a native macOS desktop app (signed arm64 DMG) for agents who prefer a dedicated window.

---

## 8. AI assist layer

AI augments agents but never acts autonomously — every AI output is editable and overridable, which protects agent trust and keeps a human accountable for what reaches the customer.

- **Auto-tagging.** On ticket creation, the model classifies the ticket into a category and suggests a priority; the agent can override both.
- **AI reply suggestions.** A button in the reply editor sends the full thread to the model and streams a draft reply back in real time for the agent to edit and send.
- **Thread summary.** Long threads (10+ messages) show an auto-generated summary panel so an agent can pick up context fast.

**Design principle:** *AI is a suggestion engine, not a decision-maker. Auto-tag confidence and reply drafts are surfaced for confirmation, never sent without a human in the loop.*

---

## 9. Non-functional requirements

**Security & privacy**
- Authentication required on all API routes; unauthenticated requests receive HTTP 401.
- Strict multi-tenant isolation — every query is scoped by workspace so no tenant can read another's data.
- Sensitive fields (e.g. password hashes) are never returned in API responses; responses use explicit field selection.
- Customer portal access is via signed, expiring tokens scoped to a single ticket.

**Reliability**
- Background jobs (SLA timers, breach alerts) run on a worker queue and degrade gracefully if the queue backend is unavailable.
- Inbound and outbound email preserve threading via message-ID headers.

**Usability & performance**
- Sub-15-minute path from signup to first handled ticket.
- Responsive web UI usable by agents on standard hardware; AI responses stream rather than block the editor.

**Data & portability**
- Reporting data is exportable to CSV for downstream analysis.

---

## 10. Release plan & phasing

The MVP was built in dependency order so each phase produced something usable before the next began.

| Phase | Theme | Scope |
|---|---|---|
| 0 | Foundations | Auth, multi-tenant workspaces, roles, and the data model. |
| 1 | Core loop | Multi-channel capture, unified inbox, ticket lifecycle, agent reply + threading. |
| 2 | Collaboration | Internal notes, @mentions, canned responses. |
| 3 | Automation & SLA | Auto-assignment rules, SLA policies, timers and breach alerts. |
| 4 | Customer portal | Signed-token portal, optional account creation. |
| 5 | Reporting | Dashboard metrics, agent leaderboard, CSV export. |
| 6 | AI assist | Auto-tagging, streamed reply suggestions, thread summaries. |
| 7 | Desktop | Electron macOS packaging (signed DMG). |

---

## 11. Risks, assumptions & dependencies

### 11.1 Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Cross-tenant data leak | Low | Critical | Query-layer workspace scoping; explicit field selection; review of all routes. |
| Email deliverability / threading breaks | Medium | High | Use managed email infra; preserve message-ID headers; monitor bounces. |
| AI reply quality is poor or off-tone | Medium | Medium | Human-in-the-loop on every draft; agents edit before sending. |
| SMBs want more features than v1 offers | Medium | Medium | Clear v2 roadmap; positioning leans into simplicity as a virtue. |
| Background-job backend unavailable | Low | Medium | Graceful degradation; SLA state still displays. |

### 11.2 Assumptions

- Email, an embeddable widget, and manual entry cover the dominant intake paths for the target segment.
- SMBs will trade feature breadth for speed-to-value and lower price.
- A no-login portal increases customer engagement versus forced account creation.

### 11.3 Dependencies

- Managed email service for inbound webhooks and outbound replies.
- Third-party AI API for the assist layer.
- A queue / cache backend for background SLA jobs (optional, with graceful degradation).

---

## 12. Open questions

| Question | Owner | Blocking? |
|---|---|---|
| What is the real channel mix for target SMBs — does email-first hold up against widget usage? | Data / Research | No |
| Is the background-job backend required in production, or is graceful degradation acceptable for SLA accuracy? | Engineering | No |
| What auto-tag confidence threshold is needed before tags apply without agent confirmation? | Data | No |
| What is the pricing model, and when should the billing UI be introduced? | Business / Stakeholder | No |
| Are data-residency / GDPR controls required for a v1 launch to EU customers? | Legal | **Yes** |
| What are the file-type and size limits for customer portal attachments? | Design / Engineering | No |

---

## Appendix A — Out of scope for v1 (v2 candidates)

Deliberately excluded from the MVP to protect the sub-15-minute setup goal and the core ticketing loop:

- Live chat widget
- Knowledge base / help center
- Phone, WhatsApp, and Slack integrations
- Native mobile apps
- Third-party marketplace integrations
- Custom fields builder
- Multi-language UI
- Public API / webhooks
- Advanced workflow automation
- CSAT survey UI
- Multi-brand support
- Billing / subscriptions UI

---

## Appendix B — Data model (entities)

Multi-tenant isolation is enforced across all entities; every record is scoped to a workspace.

`Workspace`, `User`, `Team`, `Customer`, `Ticket`, `Message`, `Activity`, `Attachment`, `SlaPolicy`, `AssignmentRule`, `Tag`, `CannedResponse`, `Invite`.
