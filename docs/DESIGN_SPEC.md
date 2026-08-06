# Blocklists for DNS
## Design Specification v1.0 — As-Built

**Author:** T. Philip — <https://github.com/t-philip>
**Date:** 6 August 2026
**Status:** Reconciled against the shipped content. Describes each list as it actually
is, including a real format defect found while writing this document and fixed before
this spec's first release.
**Licence:** Split by file — see §5. Not GPL-3.0/AGPL-3.0 like this author's code
repositories; changed 2026-08-06 after auditing where `malware_domains` actually came
from (see §5.1).

---

## 0. How to read this document

[README.md](../README.md) is the usage guide — what each list blocks and how to add it
to Pi-hole, AdGuard Home, or a hosts file. This is different from every other spec in
this author's public repos: there's no architecture to describe, because this
repository is **data, not code**. What needs explaining instead is provenance (where
each list came from), format (why it matters, and where it was wrong), and maintenance
philosophy (what "verified" can even mean for a list of domain names).

---

## 1. Purpose and scope

### 1.1 In scope

Two hosts-format domain lists for DNS-level blocking:

- **`malware_domains`** — known malware, phishing and scam domains.
- **`online_streaming_domains`** — music and video streaming services, for anyone who
  wants to block those specifically (e.g. on a shared or managed network).

### 1.2 Out of scope

- **Not a live threat feed.** There's no automated ingestion, no scheduled updates, no
  API. Entries are added when the author finds them or a reader reports one — the
  README says so plainly ("Lists are updated as and when more domains are found").
- **Not exhaustive.** `malware_domains` traces back to a single now-dead source
  (`mirror1.malwaredomains.com/files/justdomains`) plus manual additions since. It is
  one list among many a serious deployment would layer (alongside, not instead of,
  something like a maintained commercial or community feed).
- **Not deduplicated against other public blocklists.** No attempt is made to check
  whether an entry already appears in, say, StevenBlack's hosts or OISD — this list is
  additive, not a replacement for those.

---

## 2. The two lists

### 2.1 `malware_domains` (26,854 entries)

Traces back to `mirror1.malwaredomains.com/files/justdomains` — literally named
"justdomains" because that mirror shipped bare domain names with no IP prefix at all.
That's the direct explanation for why this file historically matched: it was carried
in more or less as received, never converted to hosts format. See §7.1 for why that
was a real problem, not just a style inconsistency, and what was done about it.

### 2.2 `online_streaming_domains` (96 entries, ~48 services since both `domain.com`
and `www.domain.com` are listed separately)

Hand-curated, dated to 12 September 2020 in the file's own header. Already in correct
hosts format (`0.0.0.0 <domain>`) — this file was the reference point that made §7.1
findable in the first place, since the two lists disagreed on format despite being
described identically in the README.

### 2.3 Why hosts format, specifically

Three audiences are named in the README: Pi-hole, AdGuard Home, and "any hosts-file
blocker." Pi-hole and AdGuard Home both tolerate either hosts format
(`0.0.0.0 domain`) or a bare domain list — their adlist parsers auto-detect and strip
the IP column if present. **A raw OS hosts file does not** — `/etc/hosts` (or
Windows' `drivers\etc\hosts`) requires an `<IP> <hostname>` pair on every line; a
bare domain name is not a valid entry and is simply ignored by the resolver. Hosts
format is therefore the only one of the two that satisfies all three stated audiences
at once, which is why it's the repository's actual standard (§7.1) and not merely a
style preference.

---

## 3. False-positive philosophy

Both the README and the issue templates state this explicitly, and it's worth
explaining *why* rather than just restating it: **a block list that breaks a
legitimate site gets uninstalled entirely**, taking every domain it correctly blocked
down with it. A missing malware domain is a gap; a false positive is a reason to stop
trusting the list altogether. That asymmetry is why the `false_positive.yml` issue
template exists as its own form (not folded into general bug reports) and why the
README calls false positives out as "especially welcome" ahead of new-domain
suggestions.

---

## 4. Adding and removing entries

No automation, no PR-based workflow beyond ordinary git — entries are added by
editing the file directly. The two issue templates (`add_domain.yml`,
`false_positive.yml`) exist so a reporter doesn't have to know that, or use git at
all: they ask for the domain, which list, and (for additions) evidence — a scanner
result or a description of observed behaviour — precisely because "please add this
domain" with no reasoning given is unverifiable and would either have to be trusted
blindly or ignored.

---

## 5. Licensing note — stated honestly

### 5.1 The repository does not carry one licence — it carries two, by file

Until 2026-08-06 this repository stated GPL-3.0, consistent with every other public
repo in this profile. That was reconsidered specifically for `malware_domains`, whose
provenance (§2.1, §9) makes GPL-3.0 the wrong choice: it traces back to
`mirror1.malwaredomains.com/files/justdomains`, a DNS-BH / malwaredomains.com
(RiskAnalytics) feed. Before that source went offline, its own published terms stated
the feed was "provided for free for noncommercial use," and that "any use of this list
commercially is strictly prohibited without prior approval." GPL-3.0 explicitly
*permits* commercial use — directly contradicting the terms the data was originally
offered under. Publishing it under GPL-3.0 (or AGPL-3.0, which has the same commercial-
use permission) would have been applying a more permissive licence than the author has
the standing to grant for the majority of this file's content.

**Fix.** `malware_domains` is now licensed [CC BY-NC-SA 4.0](../LICENSES/CC-BY-NC-SA-4.0.txt),
preserving the noncommercial restriction the source data actually came with.
`online_streaming_domains` (§2.2) has no such upstream restriction — it's an
independent compilation — so it is licensed separately, under
[CC BY-SA 4.0](../LICENSES/CC-BY-SA-4.0.txt), which permits commercial use. See the
root [LICENSE](../LICENSE) file for the split and [README.md](../README.md)'s lists
table for the per-file licence. Software-copyright licences (GPL/AGPL) were dropped in
favour of Creative Commons licences generally, since §5.2 below applies to *both*
files, not only the one with the inherited restriction.

### 5.2 What copyright can even claim over a list of domain names

Worth being direct about what any licence choice means here, independent of §5.1:
**a bare list of facts (domain names) is not obviously the kind of creative work
copyright protects in the first place** — in most jurisdictions, a factual list has
thin-to-no copyright on its own. Applying a licence at all is a statement of the
author's preference and the terms under which he offers the *compiled, maintained
list* (attribution requested, share-alike if redistributed, and — for
`malware_domains` — noncommercial), not a claim that the underlying domain names
themselves are original creative work. Practically: credit the author if you use or
share these, per the README and each file's licence, and don't expect a court citation
on the underlying legal question — treat it as intent, honestly stated, rather than
settled law. This reasoning does not weaken the noncommercial term in §5.1: even where
copyright is thin, the terms the original source published were explicit, and this
repository chooses to honour rather than route around them.

---

## 6. Verification status — stated honestly

There is no functional test suite for a domain list — "verified" means something
different here than for the author's code repositories.

### 6.1 What was checked

- **Format consistency**, post-fix: every line in both files matches
  `^0\.0\.0\.0 \S+$` with no exceptions — checked directly, not sampled.
- **No duplicate entries within either list** — checked with `sort | uniq -d`,
  post-lowercasing, zero found.
- **No entry appears in both lists** — checked directly; a domain correctly
  classified as streaming should not also silently appear as malware, or vice versa.
- **The domain set itself is byte-for-byte unchanged by the format fix** — the exact
  set of 26,854 domains in `malware_domains` before and after §7.1's transformation
  was diffed and found identical; only the format changed, no entry was added,
  dropped, or altered.
- **No stray whitespace, protocol prefixes, or comment lines mixed into the domain
  data** in either file.

### 6.2 What was not, and cannot be, checked from here

- **Whether any individual domain is still actually malicious, or still exists.**
  Domains in a malware list can be reclaimed, expire, or go dead; a domain-reputation
  audit of 26,854 entries is out of scope for a documentation pass and was not
  attempted.
- **Whether the streaming list is complete** for any given reader's purpose — several
  services that exist today are plausibly absent, since the last dated addition is
  from 2020.
- **Live behaviour in Pi-hole or AdGuard Home.** There's no Pi-hole or AdGuard Home
  instance available on the machine that wrote this document to load the corrected
  file into and confirm gravity/filtering picks it up as expected. The format itself
  (`0.0.0.0 <domain>`, one per line, comments with `#`) is the same format
  `online_streaming_domains` has always shipped in and the README has always
  documented as supported, so this is a low-risk claim, not a novel one — but it is
  reasoned, not directly observed against a running instance.

---

## 7. Gaps found while writing this document — and fixed

### 7.1 `malware_domains` was not actually in hosts format — **fixed**

Both the repository description and this file's own former header comment
(`hosts/README.md`) described it as hosts-format, usable directly as an OS hosts
file. It wasn't: all 26,855 lines (one of them blank) were bare domain names with no
`0.0.0.0` prefix, unlike its sibling `online_streaming_domains`, which was already
correct. Pi-hole and AdGuard Home tolerate the bare format via auto-detection, so
existing subscribers weren't broken by this — but the specific, explicit claim of
working as a **raw OS hosts file** was false for this list the entire time. See §2.3
for why that distinction is real, not pedantic.

**Fix.** Prepended `0.0.0.0 ` to all 26,854 non-blank domain lines. Also: removed the
single blank line, lowercased the 3 entries that had inconsistent casing (DNS
matching is case-insensitive in practice, but consistent casing makes future
`sort`/`uniq` maintenance reliable), and added a header comment block matching
`online_streaming_domains`'s style, since the file previously had none at all —
explaining the source, why the original format existed, and why it changed.

*Verified:* the exact set of 26,854 domains was diffed before and after the
transformation and found identical — the fix changed formatting only, not content.
Not verified against a live Pi-hole/AdGuard instance; see §6.2.

### 7.2 Two stale self-referencing URLs — **fixed**

`online_streaming_domains`'s header still pointed at
`raw.githubusercontent.com/t-philip/public/...` and
`github.com/t-philip/public` — the repository's name before the 2026-07-30
restructuring (renamed to `blocklists`). The old URL still resolves via GitHub's
rename alias (the README's own "Note on URLs" section explains this and asks readers
to migrate when convenient), so nothing was actually broken for existing subscribers
— but the file's own embedded reference to its "current" location was simply wrong.

**Fix.** Both lines updated to the `blocklists` URL and repo path.

### 7.3 `hosts/README.md` was a redundant, disorganised legacy draft — **cleaned up**

Predated the repository's current root `README.md` and was never updated to match
it: informal first-person voice ("*the list will be updated as and when I discover
more domains*"), and a structural mistake where both filenames were listed under a
"Block malware domains" heading despite one of them being the streaming list. It also
was not linked from anywhere in the current root README, so a reader browsing
normally would never have found it — its only outcome was confusing anyone who opened
`hosts/` directly on GitHub.

**Fix.** Replaced with a two-line pointer to the root README, which already covers
the same ground correctly and is the one place this information should be
maintained, rather than risking the two documents drifting apart again. Not deleted
outright — the file still exists as a legitimate, if minimal, landing point for
anyone browsing the `hosts/` folder directly.

---

## 8. Possible future work

1. **§6.2** — load the corrected `malware_domains` into a real Pi-hole or AdGuard
   Home instance and confirm gravity/filtering picks up the new format with no
   change in behaviour, closing the one claim in this document that's reasoned but
   not directly observed.
2. **§1.2** — cross-check `malware_domains` against a maintained, actively-updated
   feed (e.g. URLhaus) to identify any long-dead entries worth pruning — not
   attempted here, since the goal of this pass was format correctness, not content
   curation.
3. **§2.2** — the streaming list hasn't been reviewed for completeness since its
   2020 dating; a pass to check for services that have launched or folded since
   would keep it current.

---

## 9. Licence and provenance

Licensed by file, not as a single repository-wide licence — see §5.1 for why:

- `hosts/malware_domains` — **CC BY-NC-SA 4.0**, noncommercial only, preserving the
  restriction its upstream source published.
- `hosts/online_streaming_domains` — **CC BY-SA 4.0**, commercial use permitted.

See §5.2 for what either licence does and doesn't mean for a list of domain names.

Compiled and maintained by **T. Philip** — <https://github.com/t-philip>.
Repository: <https://github.com/t-philip/blocklists>.
`malware_domains` traces back to `mirror1.malwaredomains.com/files/justdomains`
(DNS-BH / malwaredomains.com, RiskAnalytics; no longer available).
