# Drumming GTM Engine

I run a session and touring drumming business. Over 60 days I closed 8 deals for $8,300 across sessions, touring, brand partnerships, and game scores. This is the GTM system I built to scale the one channel I couldn't leave to luck: cold outbound to game studios. It uses the same stack a startup GTM team runs on: Clay, n8n, and HubSpot.

## The problem I was actually solving

Here's my revenue by channel:

| Channel | Deals | Revenue | Share |
|---|---|---|---|
| Inbound (content / brand) | 6 | $6,300 | 76% |
| Outbound (I reached out first) | 2 | $2,000 | 24% |
| **Total** | **8** | **$8,300** | |

Plus a Zildjian brand endorsement in negotiation.

Inbound pays the bills, but I can't summon it on command. A track goes viral or it doesn't. My network is finite. The one channel I control is outbound, and it was my weakest: two deals, no system, all done by hand.

So I built the system. It finds the right studios, finds the right person inside each one, researches a real reason to reach out, scores the fit, and pushes qualified contacts into a CRM. The engine doesn't replace my inbound. It scales the channel inbound can't reach.

## The engine

Two connected pieces.

**1. Clay: research and scoring.**
A table of target game studios runs through a pipeline:
- Find the real decision-maker at each studio (audio director, sound designer, composer, or founder) by job title
- Research each studio with a gated AI agent (Claygent / Argon) that browses the web, finds a specific pitch angle (a game in development, a recent release, an audio job posting), and refuses to invent one when it can't find real evidence
- Score every studio on two axes: research confidence and how relevant the contact's role is to hiring a drummer
- Tag each lead by source (inbound vs outbound) so the funnel math stays honest

The output is a ranked list: which studios to pitch first, who to pitch, and why.

**2. n8n: the intake-to-CRM spine.**
A webhook pipeline that takes a new lead and runs it through validation before it ever touches the CRM:

Webhook → normalize → GATE: has domain? → find email (Hunter) → GATE: email found? → upsert to HubSpot
                          |                                          |
                     no domain → stop                          no email → stop

Two gates. A lead with no domain stops. A lead where no email can be found stops. Only a validated, contactable lead gets written to HubSpot, and the write is an upsert, so re-running never creates duplicates. I tested it end to end: a real lead flowed through both gates and created a contact in HubSpot with zero errors.

## Why it's built this way

Clay is the brain, n8n is the spine. Clay handles enrichment and AI research because that's what it's best at. n8n handles the reliable plumbing: intake, validation gates, idempotent CRM writes. Cramming everything into one tool would have been worse. Splitting them by what each does well is the point.

The gating is the same discipline throughout: verify before you spend, verify before you write. No AI research on junk rows. No contacts pushed to the CRM without a real email. The system prefers to reject a lead over polluting the pipeline with a bad one.

## Stack

Clay (target list, people finder, Claygent research, fit scoring) · Hunter (email finding) · n8n (webhook intake, validation gates, HubSpot upsert) · HubSpot (CRM)

## What breaks in production

- **Email coverage.** Hunter's data is thin for small indie studios, so the email gate rejects a lot of real leads. Production would waterfall across two or three email providers before giving up on a contact.
- **The email gate is strict by design.** It stops leads with no findable email, which protects the CRM but loses reachable people I could find another way (LinkedIn DM, contact form). A real system would route those to a secondary channel instead of dropping them.
- **No dead-letter yet.** Right now a rejected lead just stops. Production needs a dead-letter path (log it, alert me) so nothing disappears silently.
- **Fit score is coarse.** Two factors and a small range. More signal (studio size, recent audio hiring, soundtrack style) would spread the ranking further.
- **Cost at scale.** Free tiers cover a demo. Real volume needs a per-lead budget and a cheaper first-pass model before the expensive research runs.

## What it demonstrates

I know the difference between the channels that make me money and the channel I can actually scale, and I built the system to scale it. Enrichment, gated AI research, multi-factor scoring, a validated intake-to-CRM pipeline with idempotent writes. Real business, real revenue, real tools.
