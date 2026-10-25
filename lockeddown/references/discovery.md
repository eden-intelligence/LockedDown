# Phase 2 — Internal discovery: hosts, services, ports

**Goal:** build the Zenmap-style inventory of what's actually on the LAN — the ground
truth everything downstream depends on. Run this from a host *on* the user's network.

Confirm scope first (Phase 0): this is the user's own network. These scans are active and
noisy; that's fine on your own LAN, but keep them on the user's own subnet(s).

## 1. Find the subnet(s)

```bash
# macOS
ipconfig getifaddr en0; netstat -rn | grep default
# Linux
ip -4 addr show | grep inet; ip route | grep default
# Windows (PowerShell)
ipconfig; Get-NetRoute -DestinationPrefix 0.0.0.0/0
```

Typical home ranges: `192.168.0.0/24`, `192.168.1.0/24`, `10.0.0.0/24`. If VLANs/guest
nets exist, each is its own range — scan each separately and note which is which.

## 2. Host discovery (who's alive)

```bash
sudo nmap -sn 192.168.1.0/24          # ping sweep, no port scan
arp -a                                 # cross-check from ARP cache
```

Also pull the **DHCP lease table** from the router admin — it names devices and ties MACs
to IPs, which helps label nodes later. Cross-referencing nmap's live hosts against the
DHCP leases often surfaces devices the user forgot they had (the old tablet, the smart
bulb bridge), which are prime weak points precisely because they're forgotten.

## 3. Port & service enumeration (what each host runs)

Start light, then go deep on anything interesting:

```bash
# fast TCP sweep of the whole subnet, top ports
sudo nmap -T4 --top-ports 200 -oN lan-top200.txt 192.168.1.0/24

# deep scan of a specific host: versions + OS + default scripts
sudo nmap -sS -sV -O -sC -p- -T4 -oN host-10.deep.txt 192.168.1.10

# common UDP services (DNS, SNMP, mDNS, UPnP, NTP)
sudo nmap -sU --top-ports 50 -T3 192.168.1.10
```

Flag meanings worth narrating to the user:
- `-sV` service/version detection — the version string is what Phase 4 maps to CVEs.
- `-O` OS fingerprint — helps label and group devices.
- `-sC` default NSE scripts — surfaces default creds hints, exposed shares, weak TLS, etc.
- `-p-` all 65535 ports — slower; reserve for the crown-jewel hosts.

## 4. Identify and label every device

For each host, determine: IP, MAC (and vendor via OUI — nmap and `arp -a` show this),
hostname, device type, OS, and open services. Group into categories, because the category
drives the risk:

- **Edge/infra:** router, switch, AP, modem.
- **Crown jewels:** NAS, file server, backup target, work machine, anything holding data
  the user named in Phase 0.
- **General compute:** laptops, desktops, phones.
- **IoT/appliances:** cameras, TVs, plugs, bulbs, thermostats, voice assistants, printers.
  These are the usual *initial* foothold — rarely patched, often default-credentialed,
  always on.

Printers and IP cameras deserve special attention: they frequently expose unauthenticated
web UIs, embedded old Linux, and (printers) stored credentials for scan-to-email/SMB.

## 5. Record it structurally

Fill the asset inventory table in `assets/report-template.md` as you go — one row per
host with its services and a provisional risk note. This table is the data source for the
mermaid graphs in Phase 5, so capturing it cleanly now saves rework. Minimum columns:

`IP | hostname | type | OS/firmware | open ports (service/version) | zone | notes`

## What "weak" looks like here

- Services bound to `0.0.0.0` that only need localhost.
- Old OS/firmware versions (feed to Phase 4).
- Telnet/FTP/SMBv1/unencrypted services anywhere.
- Devices you can't identify — unknown is a finding until resolved.
- IoT devices sharing the subnet with crown jewels (hand off to Phase 3).

## 6. Enrich device identity (resolve the "unknown" rows)

An unlabeled device is a finding until resolved, and strong identification is what Fing and
Firewalla do well — match them. Beyond IP/MAC/OUI, enrich each host from the sources it
already answers on:

```bash
# mDNS / Bonjour service + model advertisement
dns-sd -B _services._dns-sd._udp            # macOS: browse advertised services
avahi-browse -art                           # Linux equivalent
# NetBIOS name (Windows / SMB devices)
nmblookup -A 192.168.1.10
# SNMP system description (printers, APs, managed switches; try default 'public' RO community)
snmpget -v2c -c public 192.168.1.10 1.3.6.1.2.1.1.1.0   # sysDescr
# UPnP friendlyName / modelName (many IoT + the router itself)
nmap -sU -p 1900 --script upnp-info 192.168.1.10
```

Combine the signals into a **make / model / role** with a confidence tag: OUI vendor + mDNS
model + UPnP friendlyName agreeing = high confidence; OUI alone = low. Record the confidence
so "unknown" either resolves or stays flagged — never leave a device unidentified and
unremarked.

## 7. Emit a structured inventory (machine-readable)

As well as the human table, capture the inventory in the JSON shape defined in
`assets/findings-schema.json`. This is what makes the **re-test diff** reliable (new device /
new port / changed service detected by comparing snapshots, not by eyeballing two reports)
and lets the data feed other tools. Save it beside the report as `inventory-<date>.json`;
the re-test skill reads the most recent one as its baseline.
