Two gates. A lead with no domain stops. A lead where no email can be found stops. Only a validated, contactable lead gets written to HubSpot, and the write is an upsert, so re-running never creates duplicates. I tested it end to end: a real lead flowed through both gates and created a contact in HubSpot with zero errors.

![n8n pipeline](n8n-pipeline.png)

![HubSpot contact created](hubspot-contact.png)

The full workflow is in this repo as `drumming-gtm-intake.json` — import it into n8n to inspect the exact build.

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
