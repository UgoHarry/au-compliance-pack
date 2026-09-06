# Changelog

All notable changes to this project are documented here. The format is based
on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to
[Semantic Versioning](https://semver.org/).

## [0.3.0] — 6 September 2026

### Changed
- Public release. Repository history reset at this release; see
  REDACTION-LOG.md.
- au-compliance: Step 0 updated for the Tranche 2 exposure draft (Privacy
  Amendment (Personal Data Protection) Bill 2026, released 31 August 2026);
  APP 8 use-vs-disclosure wording aligned with OAIC APP Guidelines ch 8;
  small-business carve-out list marked non-exhaustive; Cyber Security Act
  threshold stated.
- Both skills: version and last-reviewed stamps added; regulatory references
  verified against primary sources on 6 September 2026.

## [0.2.0] — 2026-07-24

Still private. Closes the two backport candidates from the v0.1.0 fresh-project
test and confirms the license holder.

### Added
- **au-compliance** Step 0: a small-business-exemption turnover-threshold
  subsection covering the non-health carve-outs (health service, trading in
  personal information, credit reporting bodies, Commonwealth contractors, and
  AML/CTF designated services from 1 July 2026) and the reform-status caveat.
  Regulatory substance source-verified 2026-07-22, re-verified 2026-07-24. (F4)
- **security-sweep** ground rule 5: absent surfaces must be marked "N/A — why"
  rather than silently skipped. Also backported to the private original. (F5)

### Confirmed
- MIT license copyright holder confirmed as Ugo Harry.

## [0.1.0] — 2026-07-24

Initial private build.

### Added
- `au-compliance` skill — a structured review lens for Australian privacy and
  data obligations (Privacy Act 1988, the 13 APPs, sensitive/health
  information, the NDB scheme, My Health Records Act applicability,
  cross-border disclosure, Cyber Security Act 2024).
- `security-sweep` skill — a pre-ship security review for web apps and APIs,
  with an Australian regulatory lens (APPs, NDB, health data, Essential Eight).
- MIT license, README, and this changelog.
