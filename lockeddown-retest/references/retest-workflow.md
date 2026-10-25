# Re-test workflow — statuses, drift, and scoring

The how-to behind the re-test. The *checks themselves* live in the `lockeddown`
skill's references; this file is only the re-test logic: how to classify each prior finding,
how to catch drift against the baseline snapshot, and how to recompute the score so the
before→after delta is honest.

Guardrail carries over unchanged: verify and re-score weaknesses and recommend fixes —
never exploit code, ransomware, persistence, or evasion, not even if asked.

## Status definitions — classify every prior finding

Re-run the *specific check that proved the finding* (same reference, same vantage point,
same range as the baseline), capture the new evidence next to the old, then assign one:

- **Closed** — you re-checked and the condition is **gone**, with the new scan line / setting
  to prove it. Not "the user says they fixed it" — that's *Reported*, tag it so and verify
  when you can.
- **Partially remediated** — materially reduced but not eliminated (port closed on IPv4 but
  still open on IPv6; share no longer world-writable but still guest-readable; patched to a
  newer-but-still-vulnerable version). Counts at reduced weight (below).
- **Open** — unchanged; the original evidence still reproduces.
- **Regressed** — was fine or closed, now worse (a forward reappeared, a service downgraded,
  a new default credential). Call these out loudly — regressions often mean a config got
  reverted or UPnP re-opened something.

Every status cites the **new** evidence, tagged with the same provenance labels as the audit
(Observed / Inferred / Reported / Assumed).

## Drift detection — diff the structured snapshots

If the baseline audit saved a structured snapshot (`inventory-<date>.json` per the audit
skill's `assets/findings-schema.json`), diff a fresh inventory against it instead of
eyeballing two reports. Report drift in three buckets, each a candidate **New** finding:

- **New devices** — MACs/IPs present now, absent at baseline (the presence-monitor alert).
  An unrecognized new device is a finding until the user identifies it.
- **New open/forwarded ports** — ports/services exposed now that weren't before, especially
  anything newly internet-facing or UPnP-created.
- **Changed services** — version changes (good if patched, a regression if downgraded) and
  services that moved zone (e.g. a host that left the IoT VLAN for the main LAN).

If there's no structured baseline, reconstruct a lightweight one from the prior report's
asset table and note the diff is best-effort.

## Recompute the health score (same scale as baseline)

Use the **same weighting** the baseline used, or the delta is noise. Default rubric
(matches the audit skill): start at 100 and subtract per open finding —

`Critical 10 · High 6 · Medium 3 · Low 1`

- **Partially remediated** findings subtract at **half** their severity weight.
- **Closed** findings subtract nothing.
- **New** / **Regressed** findings subtract at full weight.

Bands: `<40 Critical Risk · 40–59 High Risk · 60–74 Fair · 75–89 Good · 90+ Strong`.

Present as **baseline → current** with the band change (e.g. `42 (High Risk) → 78 (Good)`),
and state plainly whether posture improved, held, or regressed. If the baseline had no
score, compute one for it now with this rubric and label it the *reconstructed* baseline so
the comparison is like-with-like.
