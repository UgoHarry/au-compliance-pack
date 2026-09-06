# Fresh-project test — findings

The pack copies were run end-to-end against a generic project that shares none
of the author's conventions, installed **via the README's own instructions**.
Findings are logged before any fix (fixes follow, then a post-fix re-run). Some
findings suggest improvements to the *private originals* rather than the pack —
those are listed under "Backport candidates" and are deliberately **not**
applied here.

## Test setup

- **Scaffold:** a throwaway `signup-service` — plain Node/Express + `pg` +
  Postgres, ESM, one `POST /signup` route, one `GET /users` route, `schema.sql`,
  `.env.example` (Postgres URL, "US region" comment), a minimal README. No CI,
  no CLAUDE.md, no git repo, no tests, no frontend, no Supabase, no
  Stripe/Resend/LLM surfaces. Deliberately seeded with realistic gaps (plaintext
  passwords, unauthenticated table dump, open CORS, stack-trace leak, default-on
  marketing) so the skills had real material to find.
- **Install:** followed the README's project-level instructions
  (`cp -R skills/<name> <project>/.claude/skills/`). The personal-install path
  (`cp -R skills/<name> ~/.claude/skills/`) was deliberately **not** used during
  the test because it would overwrite the author's private originals; the
  project-level path is the correct one for a throwaway project regardless.
- **Runs:** each skill was executed by an independent cold-reviewer subagent
  given only the installed `SKILL.md` and the scaffold, instructed to (1) run the
  skill and produce its prescribed output, and (2) critique the skill as a
  stranger — silent assumptions, insider residue, unfollowable steps, and
  general-audience fit.

## Did the skills work?

Yes — both, on a project with none of the author's conventions:

- **au-compliance** produced a correct risk-ordered gap register: it identified
  the unauthenticated `/users` dump + plaintext passwords as launch-blockers
  under APP 11, the default-on marketing flag under APP 7 / Spam Act, the missing
  privacy policy under APP 1/5, US-region storage under APP 8, and absent
  retention/deletion under APP 11.5. It correctly ran Step 0 (concluded the
  small-business exemption plausibly applies, since no health data / no trade in
  PI) and correctly marked My Health Records Act as not applicable.
- **security-sweep** produced correct severity-ranked findings (two CRITICALs:
  plaintext passwords, unauthenticated PII/credential dump; HIGHs: open CORS,
  stack-trace leak, no rate limiting) and a correct NO-SHIP call. Its own honesty
  rule ("an honest 'not checked' beats a false 'clear'") kept the absent surfaces
  from becoming false assurances.

The *method* in both skills generalises well. The findings below are about
*framing and examples* that read as stack- or author-specific, plus one README
bug.

## Findings

### F1 — README install commands fail on a fresh target (README bug) — FIX
The README's `cp -R skills/<name> <dest>/.claude/skills/` fails when
`.claude/skills/` (or `~/.claude/skills/` on a fresh machine) does not yet
exist, because `cp -R src dest/` requires the destination parent to exist.
Confirmed empirically: the verbatim command produced
`cp: .../.claude/skills: No such file or directory` and copied nothing.
**Fix:** add `mkdir -p` before the copy in both install blocks.

### F2 — security-sweep §2 is Supabase-centric; a non-Supabase dev may skip the worst check — FIX
Section 2 ("Data-layer access patterns (Supabase/Postgres)") is written around
anon keys, service-role keys, and RLS policies. Against a plain-`pg` app with no
anon/service-role split and no RLS, every bullet was un-actionable, and the cold
reviewer warned that a plain-Postgres developer could read the Supabase framing,
conclude "this doesn't apply to me," and skip the data-layer check entirely —
which is dangerous, because the data-layer bugs (plaintext passwords, `SELECT *`
dump) were the worst ones present. The underlying principle (privileged DB
access stays server-side; the client is never trusted to enforce access) is
stack-independent. **Fix:** generalise §2 to state the stack-independent
principle first, keep Supabase/RLS as a *named example*, and give the
plain-Postgres translation so the check is never skipped. This is generalisation
of an existing check, not new content — in scope.

### F3 — au-compliance §4 aged-care parenthetical is insider residue — FIX
"**Aged care context** (relevant for B2B safety-audit products)" — the
parenthetical points at a specific product shape the author builds, not a general
audience. The surrounding B2B procurement guidance is genuinely general and
worth keeping. **Fix:** generalise the parenthetical to
"(relevant if you sell into aged-care providers)".

### F4 — au-compliance Step 0 is health-weighted; thin for the non-health case — LOG (backport candidate)
Step 0 is written almost entirely around *defeating* the small-business
exemption for health-service providers ("A medical-chart app is almost certainly
in scope"). Applied to a plain signup service, the honest answer is the opposite
(the exemption likely applies), and the skill offers no framing for that case —
the reviewer had to invent the "meet the APPs as a baseline anyway" default.
This is a real general-audience gap, but fixing it means *adding* regulatory
content (a non-health branch), which this packaging pass explicitly does not do.
**Disposition:** backport candidate, not applied. See below.

### F6 — au-compliance "medical-chart app" example reads as author-anchored — FIX
Both the initial and the post-fix cold reviews independently flagged
"A medical-chart app is almost certainly in scope regardless of turnover" as
reading like the author's own product rather than a neutral illustration (one
reviewer suggested "a health-records or symptom-tracking app" would be more
general). Not a banned name, but a twice-flagged general-audience read, and
broadening the example is a pure generalisation that preserves the exact meaning
(a health-service app holding health information is in scope despite the
small-business exemption). **Fix:** broaden to "A medical-records or
symptom-tracking app…". Verified by inspection (meaning preserved, example
broadened); not re-run through a full third pass, which would be
disproportionate to a one-phrase change already validated by two end-to-end
runs.

### F5 — security-sweep relies on the reviewer improvising "N/A" for absent surfaces — LOG (backport candidate)
Several checks assume artifacts that a given project may not have: a git repo
(secrets-in-history grep), a lockfile (`npm audit`), a client bundle
(service-role-key grep), webhooks/payments/email surfaces. The skill's honesty
rule covers this in spirit, but there is no explicit "if the project has no X,
mark it N/A rather than skipping silently" instruction. Adding one would be new
instructional content beyond generalisation. **Disposition:** backport
candidate, not applied.

## Considered and deliberately kept

- **au-compliance "medical-chart app" example** — a generic health-app category
  illustration, not a product name; useful teaching example. Kept.
- **au-compliance "solo-founder stacks" framing / "the founder"** — the pack's
  stated audience is solo and small-team AU developers, so this reads as intended
  rather than as insider residue. Kept, consistent with the README positioning.
- **Generic platform/vendor names** (Supabase/Vercel/Railway, Stripe, Resend) —
  permitted generic references per the packaging rules; kept as concrete,
  illustrative examples. F2's fix reframes Supabase as *one example* rather than
  the assumed default.
- **au-compliance "run or reference the security-sweep skill"** — a hard
  reference to the sibling skill, which is valid because this pack ships both
  skills together. Kept.

## Test-method note (not a pack defect)

The au-compliance cold reviewer noted that the skill's *registered/loaded*
description still carried the private product names while
the `SKILL.md` body it read had been genericised. This is a confound of the test
environment: the subagent ran in a session where the author's **original**
global skill (the private original, intentionally left untouched)
was also loaded into its skill registry, so it saw the original's description
alongside the redacted copy it was asked to read. The pack copy itself is clean —
verified: `grep` for the banned names returns nothing, and the installed copy is
byte-identical to the redacted pack copy. No action needed on the pack.

## Backport candidates — RESOLVED in v0.2.0

Both were closed in the v0.2.0 pass (2026-07-24). Neither is a pending candidate
any more.

1. **F4 — CLOSED (pack only).** au-compliance Step 0 gained a small-business-
   exemption turnover-threshold subsection covering the non-health carve-outs
   (health service, trading in personal information, credit reporting bodies,
   Commonwealth contractors, and AML/CTF designated services from 1 Jul 2026),
   plus the reform-status caveat (the $3M-exemption removal is a government
   commitment with no Bill and no commencement date; 10 Dec 2026 is the ADM-
   transparency date, not the exemption date). Regulatory substance source-
   verified 2026-07-22 and re-verified 2026-07-24. Applied to the **pack copy
   only** — the private au-compliance original was deliberately left untouched.
2. **F5 — CLOSED (pack + original).** security-sweep gained ground rule 5
   requiring absent surfaces to be marked "N/A — why" rather than silently
   skipped. Applied to the pack copy **and** backported to the private original
   with explicit approval. The original's only diff is the single new rule.

## Post-fix re-run

Fixes F1 (README), F2 (security-sweep §2), F3 (aged-care wording), and F6
(medical-records example) were applied to the pack copies, re-installed into the
scaffold via the README's own (now-fixed) instructions, and both skills were run
again by fresh cold reviewers.

- **F1 — README install:** the fixed commands (`mkdir -p …` before `cp`) were run
  verbatim against a fresh target with no existing `.claude/skills/`; both
  `SKILL.md` files landed at the correct paths. Resolved.
- **F2 — data-layer section:** a reviewer posing as a never-used-Supabase
  developer reported the rewritten Section 2 "actively defused any temptation to
  skip," was able to carry out every check against the plain-`pg` app, and said
  the "translate it, don't skip it" / "never 'not applicable'" wording was what
  led them to the unauthenticated `GET /users` finding. Resolved.
- **F3 — aged-care wording:** the reviewer's verdict was "General audience —
  conditional, names no product or company." Resolved.
- **F6 — health-app example:** broadened to "medical-records or symptom-tracking
  app"; the post-fix scan of the file found no product/company names in body or
  frontmatter. Resolved.
- **Skills still work:** both produced correct, complete reviews post-fix
  (au-compliance: risk-ordered gap register with the right launch-blockers;
  security-sweep: correct severity ranking and NO-SHIP call, with git-history and
  `npm audit` honestly marked N/A). No regression from the edits.

No unresolved README-instruction failures remain. Backport candidates F4 and F5
were subsequently resolved in v0.2.0 (see the resolved-candidates section above).
