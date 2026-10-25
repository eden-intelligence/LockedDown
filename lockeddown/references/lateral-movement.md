# Phase 3 — Lateral movement & segmentation

**Goal:** given a foothold on one device, measure how freely an attacker could spread.
This is the phase that decides whether a compromised smart plug stays a compromised smart
plug, or becomes "they encrypted the NAS." It's about **trust and segmentation**, not new
scanning — you're reasoning over the inventory from Phase 2.

The guiding question for every pair of devices: *if A is attacker-controlled, what can it
reach and do to B — and does B trust A more than it should?*

## 1. Map the segmentation reality

- Is the network **flat** (everything on one subnet, no isolation)? Flat is the default,
  and it means any foothold can talk to everything. This alone is usually the single
  biggest structural weakness in a home network.
- Are there **VLANs / separate SSIDs / a guest network**, and are they actually isolated
  (client isolation / inter-VLAN firewall rules on) or isolated in name only?
- Where do **IoT devices** sit relative to crown jewels? IoT on the same L2 segment as the
  NAS and the work laptop is the classic lateral-movement enabler.

Verify claimed isolation rather than trusting the label — from a device on the "isolated"
segment, test whether it can reach a host on the main segment:

```bash
# from a host on the guest/IoT segment, can it even see the main LAN?
ping 192.168.1.10
nmap -Pn -p 445,22,3389,5000 192.168.1.10
```

Reachability where there should be none is a finding; the segmentation isn't enforced.

## 2. Inventory the trust relationships that enable spread

Lateral movement in homes rides on a short, predictable list:

- **Shared/reused credentials.** The same admin password on the router, the NAS, and the
  PC means one leak unlocks all three. Reused Wi-Fi/PSK, reused local admin accounts, and
  one password manager not being used at all are the common patterns. Ask directly;
  credential reuse is often the real reason a small compromise goes network-wide.
- **Open file-sharing between hosts.** SMB (445), NFS (2049), AFP. Enumerate shares and
  their permissions:
  ```bash
  smbclient -L //192.168.1.10 -N          # null-session share listing
  showmount -e 192.168.1.10               # NFS exports
  ```
  World-readable/writable shares and guest-accessible SMB are direct lateral paths and the
  primary ransomware vector (see Phase 4 for blast-radius framing).
- **Remote-access services between hosts.** SSH (22), RDP (3389), VNC (5900), WinRM
  (5985/5986). Note which hosts expose them to the *internal* network and whether they're
  password or key/MFA protected.
- **Management planes.** The router/switch/AP/hypervisor admin interfaces, Home Assistant,
  a Pi running everything — compromise of a management plane is a shortcut to everything
  it manages.
- **Credential-caching & auto-mount.** Mapped network drives, saved SSH keys, cached
  domain/cloud creds, printers storing scan-to-SMB creds. These let a foothold inherit
  access without cracking anything.

## 3. Trace plausible paths (don't execute them)

For the top findings, write the path out in words, from a realistic entry point to a crown
jewel, naming the trust link used at each hop. You're *modeling* the path to show the user
the chain — you are not running exploitation tooling against your hosts.

Example narrative:
> Entry: internet-exposed camera (Phase 1) with default creds → camera is on the main
> flat subnet → from the camera's foothold, SMB 445 on the NAS is reachable and the NAS
> has a guest-writable share → attacker reaches the NAS and its attached backup drive.
> That single flat-network + open-share combination is the whole chain.

These narratives become the edges of the attack-path graph in Phase 5, and each one points
straight at a fix (segment the camera off; close the guest share; unique creds).

## What "weak" looks like here

- Flat network with IoT and crown jewels co-resident.
- Any guest/IoT "isolation" that fails the reachability test above.
- Reused admin credentials across devices.
- Guest/anonymous or world-writable SMB/NFS shares.
- Internal RDP/SSH/VNC with password-only auth, especially with reused passwords.
- A single management host (hypervisor, HA, router) with weak auth that controls many
  others — a high-value single point of failure.

## 4. Observe egress / outbound flows (optional — the attacker-hunt complement)

Phases 1–3 ask "how does something get *in* and spread?" This step asks the Firewalla-style
question: **what are your own hosts talking to on the way *out*** — because a device already
beaconing to a sketchy destination is how you catch an attacker who is *already inside*. It
pairs directly with the MITRE ATT&CK hunt paths in the report.

Read-only observation only — you're watching your own traffic, not touching anyone else:

```bash
# live outbound connections per host (run on the host, or read the router's flow/conn log)
netstat -tunp                      # Linux: established + listening, with pid
lsof -i -nP | grep ESTABLISHED     # macOS
# DNS is the cheapest beacon-spotter: skim resolver / Pi-hole / router query logs
```

What to look for (treat as **leads**, not verdicts): connections to raw IPs with no DNS
name, beaconing on a fixed interval, traffic to brand-new or low-reputation domains,
high-volume egress from a device that should be quiet (a bulb "calling home" to three
countries), or an IoT device talking to anything other than its vendor's cloud. Check a
suspicious destination against a public IP/domain reputation source before escalating.

A hit here feeds a **finding** ("camera at .34 beacons to <ip> every 60s — investigate") and
a **detection signal** on the MITRE paths slide. The fix framing is monitor / segment /
block at the router — never "stand up a C2 of your own to test it."
