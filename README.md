# LockedDown

A home network testing tool for Claude, by Eden Intelligence.

I built LockedDown to answer a simple question about my own network: if someone wanted in,
where would they start? It's two Claude skills. You talk to Claude, it runs the checks on
your machine, explains what it finds in plain English, and gives you a short list of what
to fix first. Come back later and it'll check whether your fixes stuck.

It's meant for networks you own or run. It finds problems and tells you how to fix them.
It won't write exploits, malware, or anything for breaking into someone else's stuff, even
if you ask.

## What's in here

- **lockeddown** is the main test. It looks at what your router exposes to the internet,
  what's on your network and what each device is running, how far someone could get if one
  device were compromised (that cheap smart plug, say), and which problems actually matter.
  You get a ranked report with network diagrams, plus a slide deck if you want one.
- **lockeddown-retest** is for after you've fixed things. It re-checks each finding from
  your last run, marks what's fixed and what isn't, looks for anything new, and shows your
  score before and after.

## Install

```bash
git clone https://github.com/eden-intelligence/LockedDown.git
cp -R LockedDown/lockeddown ~/.claude/skills/
cp -R LockedDown/lockeddown-retest ~/.claude/skills/
```

Start a new Claude Code session and it'll pick them up. If you'd rather upload skills in the
Claude app, the packaged `.skill` files are in `dist/`.

## How to use it

A full test is two passes: one from inside your network, then one from outside it. You need
both. From inside, Claude can see your devices and your router settings. Only from outside
can it see what the rest of the internet sees.

**1. Start at home.** Get on your home Wi-Fi (or plug in), open Claude, and say something
like "test my home network." Claude will confirm it's your network and ask a few questions:
what router you have, what's connected, what you'd hate to lose. Then it will:

- note your home's public IP address
- go through your router settings with you (port forwards, UPnP, remote admin)
- scan your local network and work out which devices are risky and how they connect

**2. Leave your network.** Once Claude has what it needs from the inside, it'll ask you to
switch networks. Your phone's hotspot is the easiest option. A VPN works too, but check
that your provider allows port scans. Guest Wi-Fi doesn't count, and neither does a VPN
that connects back to your house. Either way your traffic still starts at home.

**3. Let it scan from outside.** Tell Claude you've switched. It checks that your IP really
changed, then scans the home IP it saved in step 1 and nothing else. Whatever answers is
what anyone on the internet can reach. Do this in the same session, because home IPs change
now and then.

**4. Get your results.** Reconnect at home whenever you like. Claude pulls it all into a
report: what's exposed and why, diagrams of your network, and a fix list in priority order.
Ask for a slide deck if you want one.

**Later:** once you've made fixes, say "re-test my network" and lockeddown-retest checks
your work against the last report.

You don't have to do the whole thing at once. "Just check what's open from the internet" or
"just map my network" work fine on their own.

## Support

If this helped you, a star on the repo helps other people find it. You can also buy me a
coffee on Ko-fi: https://ko-fi.com/edenintelligence

## License

MIT. See [LICENSE](LICENSE). © 2026 Eden Intelligence.
