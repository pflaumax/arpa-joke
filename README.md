# A website on a `.arpa` domain

A page hosted on a domain inside `.arpa` — the top-level domain
reserved for critical internet infrastructure, which is not open to public
registration.

**Live:** [`http://cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa`](http://cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa)

<img width="787" height="797" alt="image" src="https://github.com/user-attachments/assets/8235aa11-b0b3-46f1-a940-496ce1be90fa" />

`http://` only — see [Caveats](#caveats).

## The trick

You cannot buy `mysite.arpa`. But `ip6.arpa` exists for reverse DNS lookups —
turning IPv6 addresses into names instead of the other way around — and nothing
in the specification says the zone has to be used for that. Claim a free IPv6
prefix, and its reverse-DNS zone becomes a domain you control.

Method from Ethan Hawksley's
[How to get a free .arpa domain](https://hawksley.dev/blog/get-free-arpa-domain).

## How it was built

1. **Registered at [tunnelbroker.net](https://tunnelbroker.net)** (Hurricane
   Electric) and created a Regular Tunnel. The form asks for an IPv4 endpoint
   but only validates that the address answers ICMP — it never checks ownership.
   The one used here belongs to a Japanese imageboard, picked for atmosphere.

2. **Received the routed prefix** `2001:470:1f15:4f4::/64`.

3. **Converted it to a reverse-DNS name.** Pad each group to four characters,
   split into single characters, reverse the order, append `.ip6.arpa`:

   ```
   2001:470:1f15:4f4
   → 2001:0470:1f15:04f4
   → 2.0.0.1.0.4.7.0.1.f.1.5.0.4.f.4
   → 4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa
   ```

4. **Added the zone to [deSEC](https://desec.io).** Cloudflare rejects
   `ip6.arpa` zones; deSEC accepts them.

5. **Delegated reverse DNS** in Tunnelbroker to `ns1.desec.io` and
   `ns2.desec.org`.

6. **Deployed to [Surge](https://surge.sh)** and pointed a `CNAME` at
   `geo.surge.sh` from deSEC.

Total cost: nothing.

## What's on it

A [*Steins;Gate*](https://steins-gate.fandom.com/wiki/Steins;Gate_Wiki)-themed
CERN terminal — system status readouts, a live event log, a nixie-tube-style
"Divergence Meter" clock, and a "Send D-Mail" button. The whole thing is
bilingual; a footer button switches every visible string between English and
Japanese.

Two things to find:

- **Click the wordmark** to run the whole page through an SVG displacement
  filter. Click again or press `Escape` to undo it.
- **Press `↑ ↓ ← →`** for a message about who is watching (in whichever
  language is currently active).

The Divergence Meter is a live clock rendered as CSS-only nixie tubes (no
images), formatted like the anime's worldline percentage — and its last digit
never quite agrees with real time, drifting by a fraction of a percent every
tick.

"Send D-Mail" plays out a short beat — a sending state, a random in-universe
status line, then the same 403 punchline — and logs a new entry with a real
timestamp and a `localStorage`-backed counter into the event log. Nothing is
sent anywhere; there is no server to send it to.

## Stack

One `index.html`. No build step, no dependencies, no framework, no external
requests — about 51 KB raw, ~31 KB gzipped, in a single request.

The typeface is a subset of [IPAGothic](https://moji.or.jp/ipafont/)
(IPA Font License), the Japanese free stand-in for MS Gothic, inlined as a
`data:` URI so it costs no extra request and renders both English and Japanese
from one file. The distortion is one static SVG filter (`feTurbulence` →
`feDisplacementMap`) toggled by one CSS class.

## Caveats

- **No HTTPS.** Certificate authorities will not issue certificates for
  `.arpa`, so the site is HTTP only and browsers will say it is not secure.
- **Not stable long-term.** The domain exists only while the free Hurricane
  Electric tunnel does.
- **Mildly abusive.** It repurposes a reserved zone and passes a validation
  check with an address that isn't ours. Harmless, but do not build anything
  real on it.
