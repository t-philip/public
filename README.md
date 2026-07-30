# Blocklists for DNS

Hosts-format blocklists for [Pi-hole](https://pi-hole.net), [AdGuard Home](https://adguard.com/adguard-home/overview.html),
or any DNS-level blocker (or OS) that reads a hosts file.

Licensed under [GPL-3.0](LICENSE). Please credit **t-philip** if you use or share these.

---

## The lists

| List | Blocks | Raw URL |
|---|---|---|
| **malware_domains** | Known malware, phishing and scam domains | `https://raw.githubusercontent.com/t-philip/blocklists/main/hosts/malware_domains` |
| **online_streaming_domains** | Music and video streaming services | `https://raw.githubusercontent.com/t-philip/blocklists/main/hosts/online_streaming_domains` |

`malware_domains` was originally built from `mirror1.malwaredomains.com/files/justdomains`,
which is no longer available — this list exists because that source went dark.

## Adding to Pi-hole

**Web interface:** Adlists → paste a raw URL above → Add → then Tools → Update Gravity.

**Command line:**

```bash
pihole -a adlist add https://raw.githubusercontent.com/t-philip/blocklists/main/hosts/malware_domains
pihole -g
```

## Adding to AdGuard Home

Filters → DNS blocklists → Add blocklist → Add a custom list → paste a raw URL above.

## Blocking every subdomain of a domain

A hosts list only blocks the exact entries it contains. To catch every subdomain and
path variant of a domain, use a **regex** filter instead:

| Regex | Blocks |
|---|---|
| `(^\|\.)spotify\.com(\|\.$)` | `spotify.com`, `www.spotify.com`, `open.spotify.com`, `guc-dealer.spotify.com` … |
| `(^\|\.)domain(\|.*)\.com(\|\.$)` | the above plus `sp-bootstrap.spotifycdn.com`-style variants |

In Pi-hole: Blacklist → RegEx. In AdGuard Home: Custom filtering rules.

---

## Note on URLs

This repository was previously named `public`. GitHub transparently serves the old
raw paths (`/t-philip/public/main/hosts/…`), so **existing Pi-hole and AdGuard
subscriptions continue to work unchanged**. That alias depends on no new repository
claiming the old name, so please update to the `/blocklists/` URLs above when convenient.

---

Lists are updated as and when more domains are found.
Built and maintained by [t-philip](https://github.com/t-philip).
