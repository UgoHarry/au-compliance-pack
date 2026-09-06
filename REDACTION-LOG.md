# Redaction log

Every removal / generalisation made when turning the private skills into
public-ready copies. One entry per change, `original → replacement`. This file
is a review aid.

Baseline: the initial private scaffold was a byte-identical copy of the private
originals. The public repository's history starts fresh at the public release;
the private development history was not carried over because it contained
pre-redaction content.

## Changes made

### skills/au-compliance/SKILL.md

**Frontmatter `description`, trigger sentence** — removed product names.

- original: `Trigger for <three private product names> or similar products even if the user doesn't say "compliance".`
- replacement: `Trigger for products handling health, aged-care, or financial data even if the user doesn't say "compliance".`
- reason: the three names are private product names on the banned list. The
  generic category ("health, aged-care, or financial data")
  preserves the trigger's intent for a general audience and mirrors the
  wording already used earlier in the same description.

### skills/security-sweep/SKILL.md

No changes. Grep for the banned-names list returned zero hits; the description
and body were already generic.

## Deliberately kept (judgment calls to review)

These matched a broad search but were left in place because they are generic
platform / vendor references, not Ugo's project instances. The plan explicitly
permits generic stack references ("e.g. a Node/Postgres app on a PaaS"); only
*his* deployment URLs, repo names, and project identifiers are banned.

- **au-compliance, APP 8:** "US-hosted Supabase/Vercel/Railway, LLM APIs, email
  providers" — named as illustrative offshore services, not a specific project.
  Kept as concrete, useful examples.
- **au-compliance, APP 8:** "Supabase and others offer Sydney regions" — generic
  onshoring guidance.
- **au-compliance:** "solo-founder stacks" / "the founder" framing — the pack's
  stated audience is solo AU developers, so this reads as intended, not as a
  private reference.
- **security-sweep, section 2:** header "Data-layer access patterns
  (Supabase/Postgres)" and the anon-key / service-role / RLS terminology —
  Supabase-specific vocabulary but a widely-used generic platform, kept as a
  concrete example. Flagged for the fresh-project test (Step 4): confirm it does
  not read as "everyone uses Supabase" to a cold audience.
- **security-sweep:** vendor examples "Stripe, Resend" (webhook signature
  verification) — generic, illustrative.

## Verification

- `grep -riE "<private product and entity names>" skills/`
  → no hits after this pass.
