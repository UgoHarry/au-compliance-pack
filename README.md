# AU Compliance Pack

Two [Claude Code](https://code.claude.com/docs/en/skills) skills that structure
a review of an Australian software product against the privacy and security
frameworks that apply here — the Privacy Act 1988 and its Australian Privacy
Principles, the Notifiable Data Breaches scheme, and the Essential Eight. They
are built for solo and small-team developers who are shipping to Australian
users and want a consistent lens to review against, not a certificate.

## What this is not

This pack structures a review against Australian privacy and security
frameworks (Privacy Act 1988, the APPs, the NDB scheme, the Essential
Eight). It does not certify, assess, or guarantee compliance with any
of them. Running these skills is not legal advice and is not a
substitute for professional judgement or qualified review. "Built for
the Privacy Act 1988 & APPs" is the strongest claim this project makes
about itself, and it is the strongest claim it can help you make about
your product.

## Install

Each skill is a folder under [`skills/`](skills/) containing a `SKILL.md`.
Clone or download this repository, then from the repository root copy the skill
folders to one of two locations.

**Personal (available across all your projects):** copy into `~/.claude/skills/`.

```sh
mkdir -p ~/.claude/skills
cp -R skills/au-compliance ~/.claude/skills/
cp -R skills/security-sweep ~/.claude/skills/
```

**Project-only (checked into one repo):** copy into that repo's
`.claude/skills/` instead.

```sh
mkdir -p /path/to/your-project/.claude/skills
cp -R skills/au-compliance   /path/to/your-project/.claude/skills/
cp -R skills/security-sweep  /path/to/your-project/.claude/skills/
```

If you create a top-level `~/.claude/skills/` directory that did not exist when
Claude Code started, restart Claude Code so it picks up the new directory.
Confirm the skills are installed by running `/au-compliance` or
`/security-sweep`, or by asking Claude to list its available skills.

## Usage

Both skills load automatically when Claude Code judges them relevant, and you
can also invoke either one directly by name.

**`au-compliance`** — trigger it before or during a build that handles personal
information of Australians:

> "I'm building a Node/Postgres app that stores users' health details and I'm
> hosting it on a US region. Can you run an Australian privacy review before I
> go further?"

Or invoke it directly:

> `/au-compliance` — walk this feature against the APPs.

**`security-sweep`** — trigger it before a deploy or when reviewing code that
touches auth, payments, email, or user data:

> "Review this Express signup endpoint before I deploy it."

Or invoke it directly:

> `/security-sweep` — review the changes on this branch before I ship.

## What's inside

**`au-compliance`** — a structured lens for Australian privacy and data
obligations. It works from a data inventory outward: whether the Privacy Act
applies at all (including where the small-business exemption does not save
you), a walkthrough of the 13 Australian Privacy Principles weighted toward the
ones that bite for a SaaS product, breach readiness under the NDB scheme, and
sector overlays (My Health Records Act applicability, aged-care procurement,
the Cyber Security Act 2024, the Spam Act 2003). It ends with a gap register
ordered by risk and the shortest credible path to launch-ready. It frames every
finding as a gap for you to verify, never as a settled legal conclusion.

**`security-sweep`** — a pre-ship security review for web apps and APIs. It
works through authentication and authorization, data-layer access patterns,
secrets, abuse and rate limiting, input and output handling, third-party
surfaces (webhooks, dependencies), and migration sequencing — reading the code
that proves each finding rather than assuming. It carries an Australian
regulatory lens (APPs, the NDB scheme, health data, a proportionate subset of
the Essential Eight) and ends with a severity-ranked report and an explicit
ship / no-ship call.

## Feedback

Issues are welcome — bug reports, cases where a skill missed something, or
suggestions for where the review lens could be sharper. Open an issue on the
repository.

## License

[MIT](LICENSE). © 2026 Ugo Harry.
