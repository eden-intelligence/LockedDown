# LockedDown — Home Network Testing Tool

**LockedDown** is a pair of defensive security skills for Claude that test **your own**
home network the way a security consultant would: find where it's weakest, see it the way
an attacker on the public internet would, map it, and get a prioritized plan to fix it —
then re-test to prove the fixes actually landed.

Built by **Eden Intelligence**.

> **Defensive only.** LockedDown identifies weaknesses, explains their impact, and tells
> you how to close them. It does **not** produce exploit code, reverse shells, ransomware,
> persistence, or detection-evasion — by design, even if asked. Only ever run it against a
> network you own or administer.

## What's inside

| Skill | What it does |
|---|---|
| **lockeddown** | The full test: perimeter (outside-in) → host/service discovery → lateral movement & segmentation → exploitability, CVE & blast-radius → pivotable Mermaid topology + attack-path graphs → ranked hardening report → optional consulting-style slide deck. |
| **lockeddown-retest** | Companion follow-up: loads a prior LockedDown run, re-checks each finding (Closed / Partial / Open / Regressed), sweeps for new issues and drift, recomputes the health score, and shows a before → after delta. |

Highlights: an outside-in perimeter methodology (test your own public IP from off-network),
an evidence/provenance trail for every exposure, MITRE ATT&CK hunt paths with detection
signals, a weighted health score, device-ID enrichment, CVE-reference lookup, egress/flow
hunting, baseline/drift detection, and a machine-readable findings schema.

See [`docs/competitive-comparison.md`](docs/competitive-comparison.md) for how LockedDown
stacks up against other Claude skills and tools on the market.

## Install

### Claude Code / Claude Desktop (skills folder)

```bash
git clone https://github.com/liaisb/LockedDown.git
cp -R LockedDown/lockeddown ~/.claude/skills/
cp -R LockedDown/lockeddown-retest ~/.claude/skills/
```

Reload skills, then just ask — Claude triggers LockedDown automatically on requests like
"test my home network" or "did my fixes work?"

### Prepackaged `.skill` files

Prebuilt bundles are in [`dist/`](dist/) (`lockeddown.skill`, `lockeddown-retest.skill`)
if your client installs from a `.skill` package.

## Usage

Just ask, in plain language:

- "Test my home network / find the weak points / what's exposed to the internet?"
- "Map my network and show me the attack paths."
- "Build the findings into a slide deck."
- "Re-test my network — did my fixes work?"

LockedDown confirms it's your own network before any active scan, works through its phases
conversationally, and ends in a ranked report (plus an optional deck).

## Repo layout

```
lockeddown/                     # the testing skill (SKILL.md + references/ + assets/)
lockeddown-retest/              # the companion re-test skill
docs/competitive-comparison.md  # how LockedDown compares to the market
dist/                           # prebuilt .skill packages
```

## Support this project

If LockedDown saved you some worry:

- ⭐ **Star the repo** — it genuinely helps.
- ☕ **Support on Ko-fi:** https://ko-fi.com/travisyounker
- 🧭 **Want a human to go deeper, or test a bigger environment?** Eden Intelligence does
  security consulting — reach out at `travis@eden-intelligence.com`.

## License

[MIT](LICENSE) © 2026 Eden Intelligence
