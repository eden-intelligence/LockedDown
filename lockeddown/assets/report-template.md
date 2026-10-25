# LockedDown — Home Network Assessment — <household / date>

> Scope: <the user's own home network, subnet(s)>. Audit performed with the owner's
> authorization. Defensive assessment only.

## Executive summary

Three sentences max, written for someone non-technical. What's the overall posture, what's
the single biggest risk, and what are the top 2–3 fixes that remove the most risk? Lead
with the action.

**Top priorities (do these first):**
1. <highest risk-reduction-per-effort fix>
2. <next>
3. <next>

## Network at a glance

<Insert the Phase 5 **topology map** here.>

Trust zones observed: <e.g. "single flat LAN — no segmentation"> · Crown jewels:
<NAS/backups/work laptop> · Internet-exposed services: <count + list>.

## Perimeter: entry evidence (how each foothold got there)

The services reachable from the internet, each traced from the public IP to an internal
host with the proof and the reason it exists. This is the "how they get in" ledger.

| External proof (public IP:port) | Mechanism (exact config) | Provenance | Internal landing | Creds/patch at the door |
|---|---|---|---|---|
| `<ip>:<port> open` | <port-forward rule / UPnP entry / toggle> | <deliberate / stale / UPnP-auto / default-on / ISP> | `<host>:<port>` | <default creds / fw version> |

> Keep the raw evidence (nmap block, forwarding-table paste, UPnP mapping list) below the
> table if space allows — a reader re-checking later wants the proof, not just the summary.

## Asset inventory

| IP | Hostname | Type | OS / firmware | Open ports (service/version) | Zone | Risk |
|----|----------|------|---------------|------------------------------|------|------|
| 192.168.1.1 | gateway | router | <ver> | 80, 443 (admin) | edge | <rating> |
| 192.168.1.10 | nas | crown | DSM <ver> | 445, 5000, 22 | main | <rating> |
| … | | | | | | |

## Attack paths

<Insert the Phase 5 **attack-path graph** here.>

For each path, one line per step with the enabling weakness and the fix that breaks it:

**Path 1 — <name>:** entry → … → crown jewel.
- Step 1: <weakness> → **fix:** <remediation>
- Step 2: <weakness> → **fix:** <remediation>
- *Breaking any one step defeats this path; cheapest break: <which>.*

## Findings (ranked by risk)

For each finding use this block. Keep the anchor id matching the graph node id so the
graphs can link here.

### <finding-id> — <short title>  ·  **Risk: Critical/High/Medium/Low**
- **Where:** <host / service / port>
- **What:** <the weakness, concretely — version, CVE id, default creds, exposure>
- **Likelihood:** <High/Med/Low> — <one-line why>
- **Blast radius:** <what a compromise reaches: data, hosts, backups, management plane>
- **Evidence:** <nmap line, config setting, scan result>
- **Fix:** <specific, ordered remediation steps>

<repeat, highest risk first>

## Hardening roadmap

A sequenced plan, grouped so the user isn't overwhelmed:

**This weekend (high impact, low effort):**
- <e.g. remove stale port-forward; change default creds; disable UPnP/WPS; patch router>

**This month (structural):**
- <e.g. put IoT/cameras on a separate VLAN or guest SSID with client isolation>
- <e.g. establish one offline/immutable backup unreachable from live hosts>
- <e.g. unique credentials per device via a password manager; enable MFA where available>

**Ongoing:**
- <patch cadence; replace EOL devices; periodic re-scan; monitor the always-on hosts>

## Re-test

Re-run the audit after the roadmap and confirm each prior finding is closed. Note what
changed in the topology (zones added, red nodes turned green).

## How we got to our analysis

The methodology behind the findings above, so the reader can trust them and re-run them.
See `references/methodology.md` for the full reasoning; this section records how *this*
engagement was actually done. Keep it honest — it is what separates an assessment from an
assertion.

**External perspective (how the perimeter was seen from outside).** State which vantage
point(s) produced the Phase 1 picture:
- [ ] Off-network scan of the owner's own public IP (from <cloud host / cellular>), on <date/time>
- [ ] Third-party internet-exposure data (service/engine: <which>)
- [ ] Inside-out inference from router config, confirmed from outside where possible
- CGNAT check: public IP <was / was not> in `100.64.0.0/10`.

**Data sources & tools.** List what was run and its version (e.g. `nmap 7.95`), the router
admin pages reviewed, DHCP leases pulled, and any exposure-checker used. Note the subnet(s)
scanned and that scans stayed on the owner's own ranges.

**Confidence & provenance of each finding.** Tag findings so the reader can weigh them:
- **Observed** — seen directly (scan line, config setting, share listing).
- **Inferred** — reasoned from other evidence (exposure predicted from a forward rule).
- **Reported** — stated by the owner (credential reuse, "that forward is unused").
- **Assumed** — a default adopted to proceed; these are flagged loudest.

**Limitations & caveats.** Point-in-time snapshot; dynamic IP / UPnP may change exposure;
version banners can mislead; authenticated internals of some devices were not inspected;
anything not covered this round.

> **If this is a sample/demo and not a live engagement, say so here in bold:** the data is
> representative and illustrative, constructed from realistic defaults, **not measured from
> the owner's actual network.**
