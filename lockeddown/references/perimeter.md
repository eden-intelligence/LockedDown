# Phase 1 — Perimeter: external attack surface

**Goal:** measure how hard it is for an attacker on the internet to get *any* foothold on
the network. For a home network this is dominated by a handful of things: what the router
forwards or exposes, whether UPnP is punching holes automatically, whether remote
administration is reachable, and whether anything internet-facing uses default or weak
credentials.

This phase only ever targets the user's **own public IP and own router**. Never scan
anyone else's address.

## 1. Establish the public IP and the edge device

```bash
curl -s https://api.ipify.org; echo          # your current public IPv4
curl -s https://api64.ipify.org; echo        # IPv6 if present
```

Note whether they have a single router/gateway or ISP-modem-in-bridge-mode + own router.
Double-NAT changes where the real firewall rules live. IPv6 matters: many home firewalls
NAT-protect IPv4 but leave IPv6 hosts globally routable — every device may have a public
v6 address, so check the router's IPv6 firewall separately.

## 2. What is exposed from the outside

The honest way to see external exposure is from outside the network. Two safe options:

- **Scan your own public IP from a host *not* on the network** (e.g. a cloud box, a phone
  on cellular tethering). From that external host, against *their own* IP only:
  ```bash
  sudo nmap -Pn -sS --top-ports 1000 -T3 <your-public-ip>
  sudo nmap -Pn -sU --top-ports 50  -T3 <your-public-ip>   # common UDP (VPN, DNS, etc.)
  ```
- **Use a reputable external exposure checker** the user runs themselves in a browser
  (e.g. an online port-scan / "what's open on my IP" service, or searching their own IP
  on an internet-exposure search engine). This avoids running scans and reflects what
  opportunistic scanners actually see.

Interpretation: on a well-configured home network the external scan should show **nothing
open**, or only an intentional VPN endpoint (e.g. WireGuard/UDP 51820, or an IPsec/OpenVPN
port). Anything else open to the whole internet is a finding.

Common high-severity perimeter findings:
- **Router web/admin UI reachable from WAN** (80/443/8080/8443) — remote management on.
- **SSH/Telnet (22/23) open** — Telnet is always a finding; SSH open to the world is a
  finding unless key-only and intentional.
- **RDP (3389), SMB (445), VNC (5900)** exposed — these should essentially never face the
  internet from a home.
- **NAS/camera/IoT cloud ports** forwarded directly (common: 554 RTSP cameras, 5000/5001
  Synology, 8123 Home Assistant, 32400 Plex).
- **Database ports** (3306, 5432, 27017, 6379) exposed — almost always a misconfigured
  self-hosted service.

## 3. Router configuration review (the highest-yield step)

Have the user log into the router admin (on the LAN) and walk these with them. This is
usually where the biggest wins are, and it's passive — no scanning.

- **Port forwarding / virtual servers:** list every rule. For each, ask "do you still use
  this?" Stale forwards to decommissioned services are a classic hole.
- **UPnP:** if enabled, any app or malware on the LAN can open inbound ports *without the
  user knowing*. Recommend disabling it unless something specifically needs it; if kept,
  review the active UPnP port-mapping table.
- **Remote management / "Access router from internet" / TR-069:** should be off. TR-069
  (ISP remote management, port 7547) has a history of mass-exploitation; if the ISP
  mandates it, note it as residual risk.
- **DMZ host:** if any LAN device is in the DMZ, it's fully exposed — treat as critical
  unless deliberate and hardened.
- **Firmware version & update channel:** check current version against the vendor's
  latest. End-of-life routers that no longer get security updates are a standing finding
  (recommend replacement).
- **Admin credentials:** default or weak router password is critical — it's the keys to
  the whole kingdom. Confirm it's been changed and is strong.
- **WPS:** on many routers WPS PIN is brute-forceable; recommend disabling.
- **DNS settings:** check the router's configured DNS hasn't been tampered with (rogue DNS
  is a persistence/interception technique) and note whether DoH/DoT is desired.

## 4. Wi-Fi perimeter

Wireless is a second perimeter — an attacker in range doesn't need the internet-facing
side at all.

- **Encryption:** WPA3, or WPA2-AES at minimum. WPA/WEP/TKIP or an open SSID is a finding.
- **PSK strength:** a weak/short passphrase is offline-crackable from a captured handshake.
  Recommend a long, random passphrase.
- **Guest network:** should exist and be isolated from the main LAN (client isolation on).
- **Hidden SSID / MAC filtering:** note that these are not real security controls, so
  don't let them substitute for strong encryption.

## 5. Capture the evidence trail — how each foothold got there

A perimeter finding is only useful if you can show *how* the hole exists, not just that a
port answered. For every exposure, pin down the full chain from the internet to an internal
host, with the proof at each link. This is what lets the user trust the finding, fix the
right thing, and recognize the same mistake next time — and it's what populates the "entry
point" nodes of the Phase 5 attack-path graph.

For each exposed service, record these five things:

1. **External proof** — the actual scan line or exposure-checker result showing the port
   open on the user's public IP (e.g. `3389/tcp open ms-wbt` on `203.0.113.42`).
2. **The mechanism that opened it** — the specific router/edge config responsible, quoted
   from the admin UI: the port-forward rule, the active UPnP port-mapping-table entry, the
   "remote management" toggle, the DMZ-host assignment, or the ISP TR-069 setting. This is
   the *how it got there* — name the exact rule, not just "a forward."
3. **Provenance** — why that mechanism exists: deliberate and still needed, **stale /
   forgotten** (set up once, never removed), **UPnP auto-created** by an app or malware
   without the user's knowledge, **default-on** from the vendor, or **ISP-mandated**.
   Provenance drives the fix: a stale forward is deleted, a needed one is moved behind a
   VPN, a default-on setting is turned off.
4. **Internal landing** — the `host:port` inside the LAN the exposure maps to (e.g.
   `→ 192.168.1.30:3389`), so the entry route is traceable end to end.
5. **Credential/patch state at the door** — default/weak creds or a vulnerable firmware
   version on the exposed service, which decides how cheap the foothold is (hand off to
   Phase 4 for the CVE detail).

Record it as an evidence table so the chain reads at a glance:

| External proof (public IP:port) | Mechanism (exact config) | Provenance | Internal landing | Creds/patch at the door |
|---|---|---|---|---|
| `203.0.113.42:3389 open` | Port-forward rule #4: WAN 3389 → 192.168.1.30 | **Stale** — old remote-desktop setup | `192.168.1.30:3389` | password-only, no MFA |
| `203.0.113.42:554 open` | Port-forward rule #1: WAN 554 → 192.168.1.50 | Deliberate (phone camera access) | `192.168.1.50:554` | default creds, fw w/ unauth CVE |
| `(none, LAN only) 8443` | "Access router from internet" = ON | Default-on | router `192.168.1.1:8443` | admin UI |

Where possible, keep the raw evidence verbatim (the nmap block, a screenshot/paste of the
forwarding table, the UPnP mapping list) alongside the summary — a defender re-checking in
six months wants to see the proof, not just your conclusion. Each row becomes a labeled
entry edge in the attack-path graph: the mechanism is the edge label, the provenance points
at the fix, and the internal landing is where the path continues into Phase 3.
