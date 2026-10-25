# LockedDown — competitive comparison & best-in-class plan

**Date:** 2026-10-25 · **Purpose:** position your skill against what's already out there and
pin down the upgrades that make yours best-in-class. **Part A** is the lens you asked for —
*Claude/agent skills on the market that do this*. **Part B** is the broader product landscape
(open-source tools, consumer apps, commercial appliances) for context.

---

# Part A — the Claude-skill market (skills that do this)

Narrowed to things you can actually run *as a Claude/agent skill*, not hardware or apps.
Bottom line up front: **no skill on the market does what yours does** — an authorized,
outside-in, defender-framed, full-lifecycle home-network audit that ends in ranked findings
+ pivotable graphs + a consulting deck + a re-test loop. The field splits into a thin true
analog, a same-named hollow one, a complementary blue-team skill, and a pile of offensive
red-team collections that cross the exact line your skill holds.

## A1. Direct peers — network / home-network audit skills

| Skill (where) | What it actually is | Key gaps vs yours |
|---|---|---|
| **homelab-network-auditor** — pvnkmnk/AgenticSelfHostSkills (Tessl, quality **64/100**) | The closest true analog. Audits homelab topology, scans open ports + exposed services, reviews firewall rules, generates a report; hands off to companion skills (homelab-logbook, change-planner). | Names tools generically ("nmap or equivalent") — **no concrete commands or flag mappings, no report template, no topology graphs, no outside-in/perimeter view, no lateral-movement analysis, no CVE/exploitability, no MITRE, no presentation, no post-scan validation, and no "when to use" trigger clause** (the reviewer's main dings). Your skill is a strict superset. |
| **"Network Security Audit"** — lionelsimai (skillsdirectory, graded **A / 100**) | Same *name* as the category — but the SKILL.md is a **generic planning template** (requirements → research → draft → review) that emits a components/owners/timeline table. | The "A / 100" is a **safety scan, not a capability rating.** No nmap, no scanning, no perimeter, no lateral movement, no CVE, no topology, no deck. Capability-wise it's hollow — worth knowing because the name and grade look impressive. |
| **08-network-security** — Masriyan/Claude-Code-CyberSecurity-Skill (blue-team) | Strong **defensive detection-engineering**: PCAP/tshark analysis, Suricata/Snort/Zeek rule authoring, firewall audit + report template, beaconing/DNS-tunnel/exfil anomaly detection, JA4+/QUIC. Has a clean 4-mode authorization gate (offline/passive/authorized-active/simulation). | **No active host/port discovery** (nmap only in the tool list), no CVE mapping, no MITRE mapping, no topology graphs, no presentation. It's the *detection-rule* half — complementary to yours, not overlapping. |
| **homelab-network-readiness** — affaan-m/ECC (Tessl, 79%) | A **pre-change readiness checklist** (VLAN segmentation, local DNS filtering, WireGuard remote access) before touching router/firewall/DHCP/VPN. | Checks readiness before changes; doesn't scan or assess. Complements an audit rather than being one. |
| **pentest-network-scanning** — claudskills.com (offensive) | Red-team **recon + port scanning** (Naabu, hping3). Static scan rated "B". | Offensive framing, no defender narrative, no graphs/deck/score/re-test. Crosses the weaponization line your skill deliberately holds. |

## A2. Broader security-skill collections (adjacent / mostly offensive)

- **performing-penetration-testing** (jeremylongshore) — an **orchestrator** that routes intent to 25 narrow skills, with authorization/scope checks. The orchestration pattern is the interesting bit.
- **claude-red** (0xwilliamortiz) — **58 SKILL.md across 12 domains**; authorized red-team / bug-bounty / CTF. Broad and offensive.
- **sickn33/antigravity-awesome-skills** — large collection incl. ssh-penetration-testing, a Metasploit wrapper, cloud-penetration-testing (AWS/Azure/GCP), Shodan recon.
- **Eyadkelleh/awesome-claude-skills-security** — SecLists-driven **web/injection** testing (`/sqli-test`, `/xss-test`).
- **alirezarezvani/claude-skills (security-pen-testing)** — static analysis, dependency/secret scanning, API testing, pentest report generation.
- **App/code security-audit skills** (edmdemonz9, netresearch PHP, FlorianBruniaux, wrsmith108) — backend/API/source-code security. Not network at all.

## A3. Skills-only feature matrix

Legend: ●=strong/native · ◐=partial · ○=no / out of scope.

| Capability | **Your skill** | homelab-net-auditor | "Network Security Audit" (lionelsimai) | Masriyan net-sec (blue) | pentest-network-scanning | offensive collections |
|---|:--:|:--:|:--:|:--:|:--:|:--:|
| Defensive stance (no weaponization) | ● | ● | ● | ● | ○ | ○ |
| Authorization / scope gate | ● | ● | ○ | ● | ◐ | ◐ |
| Discovery **with concrete commands** | ● | ◐ | ○ | ○ | ● | ● |
| Outside-in / perimeter view | ● | ○ | ○ | ○ | ◐ | ◐ |
| Lateral movement / segmentation | ● | ○ | ○ | ◐ | ◐ | ◐ |
| CVE / known-vuln mapping | ● | ○ | ○ | ○ | ○ | ◐ |
| MITRE ATT&CK hunt / detection | ● | ○ | ○ | ◐ | ○ | ◐ |
| Topology + attack-path **graphs** | ● | ○ | ○ | ○ | ○ | ○ |
| Risk ranking + **health score** | ● | ○ | ○ | ○ | ○ | ○ |
| Concrete **report template** | ● | ○ | ◐ | ◐ | ◐ | ◐ |
| Consulting **presentation / deck** | ● | ○ | ○ | ○ | ○ | ○ |
| **Re-test / drift** loop | ● | ◐ | ○ | ○ | ○ | ○ |
| IDS/IPS **detection-rule authoring** | ○ | ○ | ○ | ● | ○ | ◐ |
| Breadth of **offensive tooling** | ○* | ○ | ○ | ○ | ◐ | ● |

`*` out of scope by design — your edge is the defender narrative, not an exploit arsenal.

## A4. Verdict + ideas worth borrowing

**Verdict:** among Claude skills, yours is the only **defensive, homeowner-facing,
full-lifecycle** network audit (outside-in → graphs → ranked report → deck → re-test). The
nearest analog is a thin subset, the identically-named one is hollow, and the capable ones
are either blue-team detection (pairs with yours) or offensive (opposite stance, riskier
audience). Two places others legitimately beat you — **IDS/IPS rule authoring** (Masriyan)
and **raw offensive tooling breadth** (claude-red / sickn33) — are both outside your
defensive remit on purpose.

**Ideas worth borrowing** (all keep your line, optional):
1. **Execution modes** à la Masriyan — formalize your scope gate as explicit modes
   (offline / passive-own-data / authorized-active / demo-simulation). Makes the safety
   posture legible and is a pattern reviewers reward.
2. **Companion change-journal** à la homelab-network-auditor — a lightweight log of what
   changed between runs; your re-test skill is the start of this.
3. **Keep your trigger coverage** — reviewers repeatedly dinged peers for missing
   "port scan" / "nmap" trigger terms; your description already carries them, which is why
   yours fires where theirs wouldn't.

---

# Part B — the broader product landscape (hardware, apps, scanners)

## 1. The landscape — who the peers are

Nothing on the market does exactly what your skill does (an authorized, defender-framed,
outside-in audit that ends in a ranked report + pivotable graphs + a consulting deck). But
pieces of it exist across five categories:

| # | Peer | Category | What it is |
|---|------|----------|------------|
| 1 | **Nmap + NSE** | OSS engine | The scanning engine everything (yours included) is built on: host/port/version/OS discovery + `--script vuln`/`vulners` detection. Expert CLI, no interpretation, no report. |
| 2 | **NetAlertX** (ex-Pi.Alert) | OSS monitor | Self-hosted, *continuous* presence/intruder detection. Flags new devices, reconnects, IP changes; 80+ notification channels; plugins; device inventory ("network source of truth"). |
| 3 | **Fing / Fingbox** | Consumer app | Fast device discovery + identification (vendor/model, UPnP/SNMP/Bonjour/NetBIOS names), alerts, speed/trace tools. Blocking needs the Fingbox hardware. |
| 4 | **Firewalla** | Commercial box | Inline router/firewall appliance: devices + **flows** (where everything phones home, with reputation) + **alarms** + IDS/IPS ("Active Protect") + internal/external vuln scan (open ports, weak passwords) + an AI "explain this destination" button. |
| 5 | **OpenVAS/Greenbone · Nessus Essentials** | Vuln scanners | Deep CVE-matching scanners. OpenVAS runs 50k+ tests (free, heavy setup); Nessus Essentials is polished/free up to a small host cap, registration required. |
| 6 | **Agent/AI skills:** NmapMCP, nmap-mcp (Vorota), Tessl *homelab-network-auditor*, cpscript *sys-audit*, tedsluis *nmap* | AI / scripts | NmapMCP & nmap-mcp just expose nmap to an LLM (scan wrappers, no analysis). The Tessl *homelab-network-auditor* skill is the closest true analog: an agent workflow for discovery → port scan → firewall review → report — but no outside-in vantage, no attack-path graphs, no impact/MITRE framing, no deck. sys-audit is bash nmap+hydra (offensive-leaning); tedsluis draws an HTML topology, not Mermaid. |

---

## 2. Feature matrix

Legend: ●=strong / native · ◐=partial or add-on · ○=no / not its job.

| Capability | **Your skill** | Nmap/NSE | NetAlertX | Fing | Firewalla | OpenVAS/Nessus | Peer AI skills |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| Host / port / service / OS discovery | ● | ● | ◐ | ● | ● | ● | ◐ |
| **Outside-in (true external) perimeter view** | ● | ◐ | ○ | ○ | ◐ | ◐ | ○ |
| Entry-evidence trail ("how the hole got there") | ● | ○ | ○ | ○ | ○ | ○ | ○ |
| Lateral-movement / segmentation reasoning | ● | ○ | ○ | ○ | ◐ | ○ | ◐ |
| Blast-radius + ransomware/persistence as **impact** | ● | ○ | ○ | ○ | ◐ | ○ | ○ |
| **MITRE ATT&CK paths + hunt/detection signals** | ● | ○ | ○ | ○ | ◐ | ○ | ○ |
| Pivotable topology + attack-path **graphs** | ● | ○ | ◐ | ○ | ◐ | ◐ | ◐ |
| Risk ranking + weighted **health score** | ● | ○ | ○ | ○ | ◐ | ● | ○ |
| Prioritized hardening roadmap (plain-language) | ● | ○ | ○ | ◐ | ◐ | ◐ | ◐ |
| Consulting-style **report + deck** output | ● | ○ | ○ | ○ | ○ | ◐ | ○ |
| Re-test vs a prior baseline (did fixes land?) | ● | ○ | ◐ | ◐ | ◐ | ● | ○ |
| Strong **defensive guardrail** (no weaponization) | ● | n/a | n/a | n/a | n/a | n/a | ◐ |
| CVE / version→known-vuln matching | ◐→● | ◐ | ○ | ○ | ◐ | ● | ○ |
| Device-ID enrichment (mDNS/SNMP/NetBIOS/UPnP) | ◐→● | ◐ | ◐ | ● | ● | ◐ | ○ |
| Outbound **flow / egress** observation + reputation | ○→● | ○ | ○ | ○ | ● | ○ | ○ |
| Default/weak-credential exposure check | ◐→● | ◐ | ○ | ○ | ● | ● | ◐ |
| Baseline snapshot + **drift / new-device** detection | ◐→● | ○ | ● | ● | ● | ◐ | ○ |
| Machine-readable structured output (JSON) | ○→● | ◐ | ● | ◐ | ◐ | ● | ○ |
| Scheduled / continuous monitoring | ○* | ○ | ● | ◐ | ● | ◐ | ○ |
| 24/7 inline blocking / IPS | ○ | ○ | ◐ | ◐ | ● | ○ | ○ |

`→` = gap closed by the upgrades in §4. `*` = yours becomes *schedulable* (via the
schedule/loop tooling) but is not an always-on inline device — see §5.

---

## 3. Where your skill already wins

These are genuinely rare or unique in the field, and worth protecting as the core identity:

1. **Outside-in perimeter methodology.** The three-vantage approach (off-network scan of
   your own public IP → third-party exposure data → inside-out inference) is something the
   monitors and even the appliances don't really do. It's the "attacker's packet's-eye
   view," and it's your signature.
2. **Evidence & provenance trail.** "Port open *because* of this UPnP entry, which is stale,
   landing on this host" — no peer records *how the hole got there*. This is consulting-grade.
3. **Impact framing (blast radius, ransomware, persistence) + MITRE hunt paths** without
   ever crossing into weaponization. Firewalla gestures at this; nothing frames it as
   cleanly or as safely.
4. **Graphs + report + deck as one narrative.** Maltego/Zenmap-style pivots plus a ranked
   report plus a client-style deck is a storytelling layer no scanner or monitor ships.
5. **Re-test loop with a health-score delta.** Verifying that fixes landed and re-scoring
   before→after is something only the heavy commercial scanners approximate.
6. **The defensive guardrail itself** is a feature. Most "AI hacking" skills are offensive
   or ambiguous; yours holds a clear, auditable line.

---

## 4. Gaps → upgrades applied in this version

Each gap below is something a named peer does and the skill didn't fully do. All are closed
in this update, every one staying inside the defensive line (identify + detect + fix,
never weaponize):

| Gap (peer that has it) | Upgrade applied |
|---|---|
| **CVE / version→known-vuln matching** (OpenVAS, Nessus, Firewalla) | `exploitability.md`: a formal, read-only CVE-reference step — map each `-sV` version to NVD/vendor-PSIRT advisories and `nmap --script vulners`, record CVE ID + CVSS + "unauth-remote?" + fix availability. Cites advisories, never PoCs. |
| **Device-ID enrichment** (Fing, Firewalla) | `discovery.md`: enrich each host via mDNS/Bonjour, SNMP sysDescr, NetBIOS, and the UPnP friendlyName, with a fingerprint-confidence — so "unknown device" resolves to make/model/role. |
| **Outbound flow / egress visibility** (Firewalla flows) | `lateral-movement.md`: an optional, read-only egress-observation step — what your own hosts are beaconing out to, with destination reputation — as a *hunt* complement to the MITRE paths (catch a beaconing attacker already inside). |
| **Default/weak-credential exposure** (Firewalla, Nessus) | `exploitability.md`: a defensive default-credential *verification* check (did you change the shipped manufacturer default?) using documented defaults — explicitly **not** a brute-forcer; hydra/medusa/patator are out of scope. |
| **Baseline + drift / new-device detection** (NetAlertX, Fing, Firewalla) | `methodology.md` + the re-test skill: save an inventory snapshot and diff on re-test — flag new devices, new open ports, and changed services as drift. |
| **Machine-readable structured output** (NetAlertX NSoT, nmap XML, Nessus) | `assets/findings-schema.json`: a small JSON schema for the inventory + findings, so results feed other tools and make the re-test diff reliable instead of eyeballed. |
| **Broken re-test references** (internal quality) | Created the two files the re-test skill pointed at but didn't ship: `references/retest-workflow.md` and `assets/retest-report-template.md`. |
| **Scheduling for ongoing monitoring** (the monitors' core job) | `methodology.md`: a note on running the audit/re-test on a schedule for drift alerts, positioning it against always-on monitors. |

---

## 5. The honest limits (what a skill fundamentally can't be)

Worth stating plainly so the positioning is credible:

- **It is not an always-on inline device.** NetAlertX watches 24/7; Firewalla *blocks*
  inline. A skill runs when invoked (now schedulable, but still point-in-time between runs)
  and never sits in the traffic path. That's a deliberate trade: zero hardware, zero
  standing attack surface, full transparency — but not continuous enforcement.
- **CVE matching is reference-grade, not a 50k-plugin engine.** For exhaustive CVE
  coverage, the right move is to *drive* OpenVAS/Nessus and interpret their output, not to
  reimplement them. The skill's edge is the reasoning and narrative on top, not the raw
  plugin count.
- **No blocking / quarantine.** The skill recommends the fix; the user (or their
  router/Firewalla) enforces it.

The sharpest positioning: **the monitors and boxes tell you *what* changed; your skill tells
you *why it matters, how an attacker would use it, and what to do first* — and hands you a
report and a deck to act on it.** Pair it with a monitor for continuous coverage; the skill
is the brain, the monitor is the tripwire.

---

## Sources

**Part A — Claude/agent skills**
- [homelab-network-auditor (pvnkmnk/AgenticSelfHostSkills, Tessl)](https://tessl.io/registry/skills/github/pvnkmnk/AgenticSelfHostSkills/homelab-network-auditor/review)
- ["Network Security Audit" (lionelsimai, skillsdirectory)](https://www.skillsdirectory.com/skills/lionelsimai-network-security-audit)
- [Masriyan/Claude-Code-CyberSecurity-Skill — 08-network-security](https://github.com/Masriyan/Claude-Code-CyberSecurity-Skill/blob/main/skills/08-network-security/SKILL.md)
- [homelab-network-readiness (affaan-m/ECC, Tessl)](https://tessl.io/registry/skills/github/affaan-m/ECC/homelab-network-readiness/security)
- [pentest-network-scanning (claudskills.com)](https://claudskills.com/skills/pentest-network-scanning/) · [shodan-reconnaissance-and-pentesting](https://claudskills.com/skills/shodan-reconnaissance-and-pentesting/)
- [performing-penetration-testing (jeremylongshore, claudeskills.info)](https://claudeskills.info/skills/jeremylongshore/claude-code-plugins-plus-skills/performing-penetration-testing/)
- [claude-red — 58 skills across 12 domains](https://www.besthub.dev/articles/turn-claude-into-an-expert-penetration-tester-with-claude-red-58-skills-across-12-attack-domains-59eb4b317c33) · [sickn33/antigravity-awesome-skills (ssh/metasploit/cloud)](https://claudemarketplaces.com/skills/sickn33/antigravity-awesome-skills/ssh-penetration-testing)
- [Eyadkelleh/awesome-claude-skills-security](https://agentskill.work/en/skills/Eyadkelleh/awesome-claude-skills-security) · [alirezarezvani/claude-skills (security-pen-testing)](https://openskillindex.com/skills/alirezarezvani-claude-skills-security-pen-testing)

**Part B — products / open-source tools**
- [Nmap — network discovery & security auditing](https://nmap.org)
- [NetAlertX (GitHub)](https://github.com/jokob-sk/NetAlertX)
- [Fing — Network Scanner (App Store)](https://apps.apple.com/us/app/-/id430921107) · [get.fing.com](https://get.fing.com)
- [Firewalla — Cyber Security](https://firewalla.com/pages/cyber-security) · [Deep Insights](https://help.firewalla.com/hc/en-us/articles/4410153017619-Firewalla-s-Deep-Insights)
- [Nessus vs OpenVAS (Comparitech)](https://comparitech.com/net-admin/nessus-vs-openvas) · [Greenbone: OpenVAS vs Nessus](https://www.greenbone.net/en/vulnerability-scanner-openvas-vs-nessus/)
- [NmapMCP (0xPratikPatil)](https://github.com/0xPratikPatil/NmapMCP) · [nmap-mcp (Vorota, via Glama)](https://glama.ai/mcp/servers/n42cw6002f)
- [Tessl registry — homelab-network-auditor](https://tessl.io/registry/skills/github/pvnkmnk/AgenticSelfHostSkills/homelab-network-auditor) · [cpscript/sys-audit](https://github.com/cpscript/sys-audit) · [tedsluis/nmap](https://github.com/tedsluis/nmap)
