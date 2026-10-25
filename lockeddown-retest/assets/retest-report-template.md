# LockedDown Re-Test — <household>  ·  <baseline date> → <retest date>

> Re-test of the user's own home network against the prior assessment, performed with the
> owner's authorization. Defensive verification only. Same vantage points and score
> weighting as the baseline.

## Headline

One or two sentences: the score delta and how many of N findings closed.

**Health score: `<baseline>` (`<band>`) → `<current>` (`<band>`)** · Posture: **improved /
held / regressed**.

| | Count |
|---|---|
| Findings closed | `<n>` of `<N>` |
| Partially remediated | `<n>` |
| Still open | `<n>` |
| Regressed | `<n>` |
| New since baseline | `<n>` |

## Finding-by-finding status

Each row re-runs the original proving check and cites the **new** evidence.

| id | Finding | Baseline severity | Status | New evidence | Provenance |
|----|---------|-------------------|--------|--------------|------------|
| `<id>` | `<title>` | `<Crit/High/Med/Low>` | Closed / Partial / Open / Regressed | `<new scan line / setting>` | Observed / Reported |

## Drift since baseline (new attack surface)

From diffing the structured snapshots (or the prior asset table).

- **New devices:** `<mac/ip + what it is, or "unidentified — investigate">`
- **New open / forwarded ports:** `<host:port + how it got there>`
- **Changed services:** `<host: old ver → new ver — patched / regressed>`

Tag each worth tracking as a **New** finding and fold it into the ranked roadmap below.

## Before → after

<Insert the updated **topology map** and **attack-path graph** — closed nodes/edges green,
still-open red, new/regressed marked. The at-a-glance before/after is the point.>

## Remaining roadmap (re-ranked)

What's still open or new, highest risk-reduction first — the next move.

1. `<still-open or new finding>` → **fix:** `<remediation>`
2. …

## How we got to our analysis

Note this is a **re-test** (date, baseline date), the vantage points used, the confidence
tags, and any caveats (e.g. couldn't re-acquire the off-network vantage, so a perimeter
item is *Reported* not *Observed*). Keep the demo/sample caveat if the baseline was a
constructed demo.
