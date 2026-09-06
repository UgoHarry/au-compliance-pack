---
name: au-compliance
description: Australian privacy and data-regulation review for software products — Privacy Act 1988 and the 13 APPs, sensitive/health information, NDB scheme, My Health Records Act applicability, cross-border disclosure, and Cyber Security Act 2024 obligations. Use this skill whenever a product handles personal information of Australians and the user asks about compliance, privacy, consent, data retention, health data, a privacy policy, a gap analysis, or launch readiness in Australia — and proactively when reviewing any feature that collects, stores, shares, or exports user data (especially health, aged-care, or financial data). Trigger for products handling health, aged-care, or financial data even if the user doesn't say "compliance".
---

# AU Compliance

A structured review lens for Australian privacy and data obligations. Two framing rules apply to everything below:

1. **This is gap analysis, not legal advice.** Findings are flagged for the founder to verify (OAIC guidance, or a lawyer for anything load-bearing). Never state a legal conclusion as settled when it depends on facts or interpretation.
2. **Verify currency before relying on specifics.** Australian privacy law is mid-reform (Privacy and Other Legislation Amendment Act 2024 introduced a statutory tort for serious invasions of privacy, doxxing offences, and automated-decision transparency requirements with staged commencement; a broader second tranche has been foreshadowed). Where a finding turns on a recent or pending change, say so and check current OAIC/legislation sources rather than trusting recalled detail.

## Step 0 — Does the Privacy Act even apply?

Don't assume the small business exemption saves you. Annual turnover ≤ $3M generally exempts a business, **but the exemption does not apply to** (among others):
- **Health service providers holding health information** — and "health service" is broad: an app that assesses, records, or manages health information for individuals can qualify. A medical-records or symptom-tracking app is almost certainly in scope regardless of turnover.
- Businesses that trade in personal information (buy/sell/disclose for benefit).

State the applicability conclusion explicitly, with reasoning, and flag it for legal confirmation if it's arguable. If the Act applies, everything below applies.

### The turnover threshold and its carve-outs

If annual turnover is under AUD $3 million, the small-business
exemption MAY apply — but check the carve-outs before relying on it,
because they remove the exemption regardless of turnover:

- You provide a **health service** and hold health information (this
  is broader than clinics — a wellness app storing symptoms can
  qualify).
- You **trade in personal information** (buy/sell/disclose it for
  benefit).
- You are a **credit reporting body** or a **Commonwealth contractor**.
- From 1 July 2026: you provide **AML/CTF designated services**
  (accounting, legal, conveyancing, real estate) — covered for your
  AML-related data handling.

Two common errors, in both directions:
1. Health-adjacent builders assuming the exemption covers them — the
   health-service carve-out usually strips it.
2. Treating the exemption's removal as law — as at July 2026 it is a
   government commitment, not legislation, and it has NO commencement
   date. (10 December 2026 is the ADM-transparency date, which some
   commentary wrongly attaches to the exemption.) Re-verify current
   status at review time: the reform is actively progressing.

If the exemption applies, note it in the review with the reasoning —
and still consider the APPs as practice: voluntary opt-in exists, the
reform direction is one-way, and customer expectations don't read
s 6D.

Status of this section verified 22 July 2026 against OAIC guidance
and current commentary. If that date is old, re-verify before relying
on it.

## Step 1 — Data inventory (do this before assessing anything)

Build a table: every category of personal information collected → where it's stored (service + region) → why it's needed → who can access it → how long it's kept → how it's deleted. Every subsequent finding hangs off this table. If the inventory can't be completed from the codebase and docs, that's finding #1.

Classify anything that is **sensitive information** (health, racial/ethnic origin, sexual orientation, biometrics, etc.) — it carries a higher bar throughout.

## Step 2 — APP walkthrough

Assess against the 13 Australian Privacy Principles. Weight effort toward the ones that bite for a SaaS product:

- **APP 1 (open and transparent management):** privacy policy exists, is accurate against the data inventory (not a template that claims things the product doesn't do), and is findable.
- **APP 3 (collection):** collect only what's reasonably necessary. **Sensitive information requires consent** — check the actual consent flow in the UI, not the policy's claim of one. Consent must be informed, current, and specific; a pre-ticked box or buried clause doesn't cut it.
- **APP 5 (notification):** at or before collection, users are told what's collected, why, and who it goes to.
- **APP 6 (use and disclosure):** data used only for the purpose collected, or a directly related purpose the user would reasonably expect. Check analytics, marketing, and AI-model usage against this.
- **APP 7 (direct marketing):** opt-out that works; sensitive info not used for marketing without consent.
- **APP 8 (cross-border disclosure):** map every offshore service in the inventory (US-hosted Supabase/Vercel/Railway, LLM APIs, email providers). OAIC guidance generally treats sending personal information to an overseas provider as disclosure — the entity remains accountable for the overseas recipient's handling unless a narrow exception applies. Options: contractual measures + due diligence, informed consent, or onshore hosting (Supabase and others offer Sydney regions — for health data, seriously consider it). This is routinely the biggest gap in solo-founder stacks; check it every time.
- **APP 11 (security):** reasonable steps to protect — run or reference the security-sweep skill; also covers **destruction/de-identification when no longer needed**, which means a retention schedule and a working deletion path (including backups strategy for deleted data). "We never delete anything" is a finding.
- **APP 12/13 (access and correction):** a practical way for a user to get their data and correct it. Doesn't need to be automated; does need to exist and be described in the policy.
- APPs 2, 4, 9, 10 (anonymity option, unsolicited info, government identifiers, data quality): check briefly; flag only if relevant.

## Step 3 — Breach readiness (NDB scheme)

- Could this product suffer an **eligible data breach** (unauthorised access/disclosure likely to result in serious harm)? For health data the answer is presumptively yes.
- Requirements when one occurs: assess suspected breaches promptly (the Act allows up to 30 days for assessment), and notify the OAIC and affected individuals as soon as practicable once an eligible breach is confirmed.
- Readiness checklist: detection capability exists (logging/monitoring — you can't notify what you can't see), a one-page response plan names who assesses, how users are contacted, and where the OAIC form lives. Absence of a plan is a finding even if security is strong.

## Step 4 — Sector overlays (apply the ones that fit)

- **My Health Records Act:** applies to participants in the MHR system (registered portal operators, connecting software, healthcare providers using MHR). If the product does not connect to My Health Record, the Act likely doesn't apply — but state that conclusion explicitly, because if the product ever integrates, a materially stricter regime (including its own mandatory breach reporting to the System Operator/OAIC) switches on. Treat "should we integrate with MHR" as a compliance decision, not just a feature decision.
- **Aged care context** (relevant if you sell into aged-care providers): the direct obligations usually sit with the provider-customer, but expect their procurement to push privacy, security, and data-handling requirements down via contract — being able to answer a privacy/security questionnaire well is a sales asset. Verify current Aged Care Act arrangements if the product touches care recipients' personal information directly.
- **Cyber Security Act 2024:** the ransomware payment reporting obligation (72-hour report) applies above a turnover threshold — likely not yet applicable to a bootstrapped company, but note it in the register with its trigger condition rather than ignoring it.
- **Spam Act 2003:** any marketing email needs consent, sender identification, and a functional unsubscribe — cheap to check, embarrassing to breach.

## Output format — gap register

Produce a register the founder can work through (consistent with a phased remediation approach):

```
# | Gap | Obligation (APP/Act) | Risk if unaddressed | Fix | Effort (S/M/L) | Blocking launch? | Verify with lawyer?
```

Rules for the register:
- Order by risk, not by APP number.
- "Blocking launch?" gets a real yes/no. Collecting health information without a working consent flow: yes. Privacy policy missing a correction-request paragraph: no.
- Distinguish **facts** (the code does X), **inferences** (X likely means Y under APP Z), and **assumptions** (turnover below threshold) — and mark which findings depend on assumptions.
- End with the shortest credible path to launch-ready: the minimum ordered subset, not the full register at once.
