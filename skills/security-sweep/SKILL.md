---
name: security-sweep
description: Pre-ship security review for web apps and APIs, plus an Australian regulatory compliance lens (Privacy Act/APPs, NDB, health data, Essential Eight). Use this skill BEFORE any deploy, release, or "ship it" moment, whenever the user asks to review code for security, audit an endpoint or feature, check a PR, or mentions shipping, launching, deploying, or going live — even if they don't say the word "security". Also use when reviewing anything touching auth, payments, email sending, user data, or Supabase/Postgres access patterns.
---

# Security Sweep

Everything that ships gets checked. The goal is to find the vulnerability before a stranger does, and to never say "looks fine" without having read the code that proves it.

## Ground rules

1. **Evidence over assumption.** Never assert an endpoint is protected without reading the actual middleware/guard code on that route. "The other routes use auth so this one probably does" is how unauthenticated routes ship.
2. **Read the diff AND the blast radius.** A change to a shared constant, client factory, or middleware affects every consumer. Trace outward from the diff.
3. **Attacker mindset, concretely.** For each finding, write the actual exploit scenario: who does what with which request, and what they get. If you can't articulate the exploit, downgrade the severity honestly.
4. **No security theatre.** Don't pad the report with generic advice ("consider using HTTPS"). Every finding must be specific to this codebase.
5. **Absent surfaces get an explicit N/A, never silence.** If a section doesn't apply (no payments, no email sending, no file uploads), write "N/A — <one line why>" for it in the review output. A sweep with explicit N/As is auditable; a sweep with gaps is indistinguishable from an unfinished one.

## The sweep

Work through these in order. For each area, name the files you inspected.

### 1. Authentication & authorization
- Every route that mutates data or sends anything (email, SMS, webhooks out): does it verify identity? List routes and their guards explicitly.
- Unauthenticated side-effect routes are critical severity by default (an open email-send route = spam relay + sender-reputation destruction).
- Authorization ≠ authentication: does the authenticated user own the resource they're touching? Check for IDOR on every `:id` parameter.

### 2. Data-layer access patterns
The principle is stack-independent: privileged database access lives server-side, and the client is never trusted to enforce who can read or write what. Apply it to whatever you use — a managed platform with row-level security (e.g. Supabase), a plain Postgres connection from your own server, or another database entirely. If a project has none of the platform features named below, the check still applies — translate it, don't skip it. The data-layer bugs are often the worst ones (an unauthenticated table dump, plaintext columns), so this section is never "not applicable".
- **Privileged credentials stay server-side.** Any key or role that bypasses access rules (a Supabase service-role key, a superuser database URL, an admin API token) must never reach the browser or a client bundle — grep the client bundle for them. Client code gets a scoped/anon credential only.
- **Trusted writes go through the server.** Inserts/updates that must be trusted (signups, payments, entitlements) run through server-side routes, never from the browser. On a platform with row-level security this means privileged writes use the server-side role; on plain Postgres it means the browser never holds the DB connection and the route enforces it.
- **Row-level access, per table.** For each table holding user data, state how access is restricted to the owning user and whether it is actually enforced. On Supabase/Postgres that is an RLS policy that is *enabled* (a written-but-not-enabled policy counts as no policy); on a plain-Postgres API with no RLS, the auth + ownership check in the route stands in for it — confirm one or the other exists for every table.

### 3. Secrets
- Grep the repo and git history for keys (`sk-`, `api_key`, `SUPABASE_SERVICE`, `STRIPE_SECRET`, `.env` committed). A key that touched git history is compromised — rotation, not deletion, is the fix.
- Confirm `.gitignore` covers env files and that example env files contain placeholders only.

### 4. Abuse & rate limiting
- Any endpoint that costs money per call (LLM calls, email sends, SMS) or creates records must be rate limited. Prefer durable rate limiting (Postgres-backed or equivalent) over in-memory, which resets on deploy and doesn't survive multiple instances.
- Check signup/auth endpoints for enumeration and brute-force exposure.

### 5. Input & output
- Validate inputs server-side (types, lengths, allowed values) — client validation is UX, not security.
- Check error responses for leakage: stack traces, SQL fragments, internal paths, "user not found" vs "wrong password" distinctions.

### 6. Third-party surfaces
- Webhooks in (Stripe, Resend, etc.): signature verification present and tested? An unverified webhook endpoint lets anyone grant themselves entitlements.
- Dependencies: run `npm audit` (or equivalent); triage criticals, don't just report the count.

### 7. Migrations & sequencing
- If the change involves schema + code, verify the deploy sequence: can the old code run against the new schema during rollout? State the safe order explicitly.

## AU compliance lens

Apply when the product handles personal information of Australians. This is a review lens, not legal advice — flag issues for the founder to verify, don't assert legal conclusions.

- **APPs (Privacy Act 1988):** collection minimisation (are you storing more than the feature needs?), APP 11 security of personal information, APP 12 access, retention/deletion path exists.
- **NDB scheme:** if this data leaked, would it likely cause serious harm? If yes, confirm there's a way to even *detect* a breach (logging, monitoring) — you can't notify what you can't see.
- **Health information** (e.g. medical charts, health app data): treated as sensitive information under the Privacy Act — higher bar for consent and security; check whether My Health Records Act obligations could apply before touching MHR data.
- **Essential Eight (proportionate):** for a solo SaaS, the relevant subset is patching (dependency currency), MFA on admin surfaces (hosting, DB, domain registrar, email), restricted admin privileges, and backups — verified restorable, not just configured.

## Report format

Rank findings by severity. For each:

```
[CRITICAL|HIGH|MEDIUM|LOW] <one-line title>
Where: <file:line or route>
Exploit: <concrete scenario — who, what request, what they gain>
Fix: <specific change, with code if short>
Verify: <the command, test, or request that proves the fix works>
```

End with: files/routes inspected, and anything you could NOT verify (and why). An honest "not checked" beats a false "clear".

Ship/no-ship call: give one. Criticals and unauthenticated side-effect routes block shipping; be explicit rather than diplomatic.
