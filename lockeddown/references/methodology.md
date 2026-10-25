# Methodology — how this audit reaches its conclusions

This file is the "how-to" behind the audit: the reasoning that produces each finding, so
the analysis is reproducible and defensible rather than a pile of assertions. Read it
before running an audit, and lean on it hardest for Phase 1, where the central trick is
seeing your own network the way an outsider does.

## The core problem: you're inside the castle, but the attacker is outside it

Almost everything you can run (nmap, the router UI, `ip addr`) runs from *inside* your
network, behind the router's NAT and firewall. But the perimeter question — "how hard is
it to get a foothold from the internet?" — is an **outside-in** question. From inside, you
see the doors as they look from the hallway; the attacker sees them from the street. Those
two views disagree, and the gap is exactly where surprises live (a port you think is
closed is forwarded; a device you forgot has a public IPv6 address).

So the method for Phase 1 is deliberately about *acquiring the outside view*, by one of
three vantage points, in rough order of fidelity:

### Vantage point 1 — Scan your own public IP from off-network (the truest view)

Run the scan from a host that is **not on your LAN** — a cloud VM, or your laptop/phone on
cellular tethering — pointed at *your own* public IP. This is literally the attacker's
packet's-eye view: it traverses the same internet path and hits the same firewall from the
same side. What answers here is what the world can reach.

```bash
# from an OFF-network host, against YOUR OWN public IP only
sudo nmap -Pn -sS --top-ports 1000 -T3 <your-public-ip>
```

This is the gold standard because there's no inference — a port is open or it isn't, as
seen from outside. Its limits: it's a point-in-time snapshot (a dynamic IP or a
UPnP-opened port can change), and CGNAT (carrier-grade NAT) can mean your "public" IP is
shared and not truly yours to scan — note that if the public IP is in `100.64.0.0/10`.

### Vantage point 2 — Read third-party internet-exposure data (no scanning by you)

Query what internet-wide scanners have *already* seen about your IP: an online port-check
service, or an internet-exposure search engine's record of your address. This reflects
what opportunistic, mass-scanning attackers actually index — which is the real threat for a
home — and it costs you no packets. Its limit: it can be stale or incomplete, so treat it
as corroboration, strongest when it *agrees* with vantage point 1.

### Vantage point 3 — Infer from the inside, then confirm from the outside

When you can't get off-network, reason outward from the router's own configuration: the
port-forwarding table, the UPnP mapping table, the DMZ assignment, and the remote-admin
toggles *are* the rules that create external exposure. Read them and you can predict what
*should* be reachable from outside — then confirm as many as you can with vantage point 1
or 2. This is also how you establish **provenance** (Phase 1's evidence trail): the forward
rule is the *mechanism*, and whether it's stale/UPnP-made/default-on is the *why*. Inside
inference explains the "how it got there"; the outside scan proves the "it's really open."

The strongest Phase 1 finding uses all three: the router config says a rule exists (why),
the off-network scan confirms the port answers (proof), and the exposure engine shows it's
already indexed (it's being found). Converging sources = high confidence.

## Reasoning the rest of the audit

The later phases are inside-the-network work, but the same discipline applies: every
conclusion should trace to evidence, and you should label how sure you are.

- **Phase 2 (discovery)** is direct observation — hosts and banners you actually saw. High
  confidence, but a version banner can lie, so treat it as a lead, not proof of a CVE.
- **Phase 3 (lateral movement)** is partly *tested* (you ran the reachability check from
  the guest segment) and partly *reasoned* (you infer a credential is reused because the
  user said so). Separate what you verified from what you were told.
- **Phase 4 (exploitability)** maps observed versions to public CVE data. The exploitable
  *condition* is a fact; whether a specific box would actually fall is a judgment — say so,
  and never cross into proving it with a working exploit (that's the skill's firm line).

## Label every conclusion by how you know it

Carry a confidence/source tag on findings so a reader can weigh them:

- **Observed** — you saw it directly (a scan line, a config setting, a share listing).
- **Inferred** — you reasoned it from other evidence (exposure predicted from a forward
  rule; blast radius deduced from the trust map).
- **Reported** — the user told you (credential reuse, "I don't use that forward anymore").
- **Assumed** — a reasonable default you adopted to keep moving; flag these loudest,
  because they're where you're most likely wrong.

This labeling is what feeds the report's "How we got to our analysis" section — it turns
the audit from a verdict into a transparent argument the user can check and re-run.

## When you don't have live data (demos, examples, pre-engagement walkthroughs)

If you're producing a *sample* run with no real network to scan — a demonstration, a
teaching example, a template to show a client — then your data is **constructed**, not
observed. Say that plainly and up front. Build the sample from realistic defaults (common
ISP-router behavior, typical IoT exposure, default credentials that ship in the wild) so
it's instructive, but never present synthesized findings as though they came from the
user's actual network. The honest provenance of a demo is "representative, illustrative,
not measured," and the report must carry that caveat.

## Baseline, drift, and running this on a schedule

A single audit is a snapshot; the monitors it competes with (NetAlertX, Fing, Firewalla)
earn their keep by watching *over time*. You get most of that value without standing
hardware by treating each run as a **baseline** and diffing the next one against it:

- Every audit saves its structured inventory + findings (see `assets/findings-schema.json`,
  written as `inventory-<date>.json` / `findings-<date>.json`). That snapshot *is* the
  baseline.
- The **lockeddown-retest** skill diffs a fresh run against the latest snapshot and
  reports **drift**: new devices that appeared, new open/forwarded ports, services that
  changed version, and findings that regressed. New-device and new-port drift is exactly
  what the presence monitors alert on.
- For ongoing coverage, the audit (or a light perimeter-only re-test) can run on a
  **schedule** — e.g. a weekly perimeter check and a monthly full re-test — using the
  session's scheduling/loop tooling, surfacing the drift as the alert. This makes the skill
  *schedulable* rather than always-on inline: no hardware, no standing attack surface, but
  not a substitute for an inline IPS if the user wants real-time blocking.

Keep the positioning honest: the skill is the analyst that explains *why it matters and what
to do*; a continuous monitor is the tripwire. Recommend pairing the two when the user wants
24/7 coverage.
