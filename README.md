# A website on a `.arpa` domain

A one-page joke hosted on a domain inside `.arpa` — the top-level domain
reserved for critical internet infrastructure, which is not open to public
registration.

**Live:** [`http://cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa`](http://cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa)

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

A fake *Steins;Gate*-themed CERN terminal — system status readouts, an event
log, a "Send D-Mail" button that fails with `ERROR 403: Try again yesterday`,
and a damage report counting gelatinized bananas.

Two things to find:

- **Click the wordmark** to run the whole page through an SVG displacement
  filter. Click again or press `Escape` to undo it.
- **Press `↑ ↓ ← →`** for a message about who is watching.

The joke is the contrast: the address looks like production infrastructure, the
contents do not.

## Stack

One `index.html`. No build step, no dependencies, no framework — about 5 KB
gzipped in a single request. The distortion is one static SVG filter
(`feTurbulence` → `feDisplacementMap`) toggled by one CSS class.

## Deploying

The deploy target lives in an untracked `CNAME` file. Recreate it once:

```bash
echo "cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa" > CNAME
```

Then publish from the project directory:

```bash
npx surge . cern.4.f.4.0.5.1.f.1.0.7.4.0.1.0.0.2.ip6.arpa
```

Run it from the project root. Running `npx surge` from your home directory makes
it scan all of `$HOME` and fail on macOS with
`EPERM: scandir 'Library/Accounts'`.

## Caveats

- **No HTTPS.** Certificate authorities will not issue certificates for
  `.arpa`, so the site is HTTP only and browsers will say it is not secure.
- **Not stable long-term.** The domain exists only while the free Hurricane
  Electric tunnel does.
- **Mildly abusive.** It repurposes a reserved zone and passes a validation
  check with an address that isn't ours. Harmless, but do not build anything
  real on it.

## Credit

The technique is Ethan Hawksley's, who in turn found it via
[a post on hijacking `e164.arpa`](https://lina.sh/blog/hijacking-e164-arpa).

*El Psy Kongroo.*
