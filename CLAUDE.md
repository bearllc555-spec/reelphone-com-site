# reelphone.com — Project Handoff

This file is read at the start of every session that touches reelphone.com. It captures every system, credential reference, file path, and architectural decision so a fresh Claude session (or Anthony, or a future operator) has full context without re-deriving anything. Lives at the repo root so it travels with the codebase.

---

## Status (as of 2026-05-13)

**LIVE** at https://reelphone.com/. SSL active. Apex attached to Cloudflare Pages project `reelphone-com`. www mirror not yet wired (TBD — see "What's deferred / open").

**Brand:** standalone Bear LLC digital property. Mercury-clean splash, same visual system as 998webdesigns / webhosting197. **NO Slatepress reference anywhere.** Footer attribution is `a bear llc digital property` only. This is intentional — reelphone is positioned as its own brand. Slatepress framing stays out.

**Offer:** NOT YET DEFINED. README explicitly says so. The splash is a pure coming-soon placeholder while Anthony figures out what reelphone actually is (the name implies video + phone, the logo reinforces that, but no product spec exists yet). Do not put offer copy, pricing, or feature promises on the splash until Anthony locks the offer.

**Site content (current splash):**
- Phone-with-play-button SVG logo (inline, currentColor, ink-black)
- Wordmark: `reelphone v01` (lowercase, 30px Inter 800, tiny slate v0X tell)
- Eyebrow pill: `in build . coming soon` with pulsing blue dot
- H1: *"something is coming."* (lowercase, with blue accent on "coming.")
- Lead: "a new bear llc property. quietly under construction."
- Footer: "reelphone.com (c) 2026 . a bear llc digital property"
- No CTA, no mailto, no email capture. Splash is informational only until offer lands.

---

## Quick links

| Surface | URL |
|---|---|
| Live site | https://reelphone.com/ |
| CF Pages preview | https://reelphone-com.pages.dev/ |
| Source repo (public) | https://github.com/bearllc555-spec/reelphone-com-site |
| GitHub Actions | https://github.com/bearllc555-spec/reelphone-com-site/actions |
| CF Pages project | https://dash.cloudflare.com/e0f6f68f26f8a26a75eaa793385019ef/pages/view/reelphone-com |

---

## Domain & DNS

- **Domain:** `reelphone.com`
- **Registrar:** TBD — verify on first session that needs DNS changes. Likely Hostinger like the other Bear LLC properties; check the registrar nameserver record before assuming.
- **Nameservers (at registrar):** Cloudflare-assigned pair. Always verify the exact pair on the zone's "Update your nameservers to activate Cloudflare" screen BEFORE re-pointing — wrong pair causes silent SERVFAIL "lame delegation" for days.
- **Cloudflare zone:** active (apex resolves and SSL is live). ZoneID not captured here — look up at https://dash.cloudflare.com/e0f6f68f26f8a26a75eaa793385019ef before any DNS work.

**DNS records (Cloudflare) — presumed minimum:**

| Type | Name | Content | Proxy | Purpose |
|---|---|---|---|---|
| CNAME | `@` (apex) | `reelphone-com.pages.dev` | Proxied (orange) | Apex -> CF Pages |

`www.reelphone.com` is not yet attached as a custom domain on the Pages project. Add it when the brand is ready for an offer launch (mirror the 998webdesigns pattern: second CNAME + second custom-domain attach on the Pages project).

No mail records yet. The splash has no `mailto:` CTA. If `hello@reelphone.com` is ever needed, add reelphone.com as a Google Workspace domain alias under the slatepress.co tenant (same pattern as plumbingslatepress.com — see `slatepress/CLAUDE.md` "Wired & working" section).

---

## Cloudflare account

- **Account ID:** `e0f6f68f26f8a26a75eaa793385019ef`
- **Account email:** `bearllc555@gmail.com`
- **Pages project:** `reelphone-com`
- **Custom domains attached:** `reelphone.com` (apex). `www.reelphone.com` not yet attached.
- **SSL:** Active on apex.
- **Deploy mechanism:** **git-wired auto-deploy** via `cloudflare/wrangler-action@v3` on push to `main`. Direct Upload zip-drag is not used for this site.

---

## GitHub repo

- **URL:** https://github.com/bearllc555-spec/reelphone-com-site
- **Visibility:** public
- **Default branch:** `main`
- **Owner:** `bearllc555-spec`

**Auto-deploy workflow** at `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Cloudflare Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      deployments: write
    steps:
      - uses: actions/checkout@v4
      - uses: cloudflare/wrangler-action@v3
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          command: pages deploy . --project-name=reelphone-com
```

**Required repo secrets (already set):**

- `CLOUDFLARE_API_TOKEN` — token name `plumbingslatepress-com-site-github-actions` (reused across all bearllc555-spec Pages projects; scope: Account -> Cloudflare Pages -> Edit). Stored locally at `slatepress/.local/cf-pages-token.txt`.
- `CLOUDFLARE_ACCOUNT_ID` — `e0f6f68f26f8a26a75eaa793385019ef`

**PAT used for git push:**

- `slatepress-demos-deploy` — broader-scope PAT, all repos under `bearllc555-spec`. Stored at `slatepress/.local/slatepress-demos-deploy-pat.txt`.
- **Limitation:** PAT does NOT include `Workflow write` scope. Cannot push or modify `.github/workflows/*` files. Workflow file edits go through the **GitHub web UI** (one-time concern; the workflow file is stable).

There is also a legacy one-shot script `slatepress/PUSH-REELPHONE.cmd` from the initial repo bootstrap. It works but is rigid (always tries to make a fresh `initial:` commit), so on day-to-day pushes prefer the bash-sandbox flow below.

---

## Local workspace layout

The repo is cloned locally at:

```
C:\Users\thede\OneDrive\Documents\Claude\slatepress\repos\reelphone-com-site\
|-- CLAUDE.md            <- this file (project context)
|-- README.md            <- one-line public repo overview
|-- index.html           <- the single splash page
|-- favicon.svg          <- blue-slash favicon (shared mark with slatepress/998)
`-- .github/
    `-- workflows/
        `-- deploy.yml   <- auto-deploy on push to main
```

---

## How to edit and ship a change

There are two workflows. **Cursor is the default for any code work as of 2026-05-11.**

### Path A — Cursor (default, code work)

Open Cursor at `C:\Users\thede\OneDrive\Documents\Claude\slatepress\repos\reelphone-com-site\`. Pull latest. Edit. Bump `v0X` in `index.html`. Feature branch first (e.g. `polish/logo-tweak`), push, review the auto-built preview at `<branch-slug>.reelphone-com.pages.dev`, merge to `main` to ship.

### Path B — Cowork bash sandbox (fallback)

The Cowork bash sandbox can edit and push, but **OneDrive Files-On-Demand actively corrupts `.git/` index files** when bash and Windows touch the same repo concurrently. This session burned several minutes on stale-stat / corrupt-index recovery before a clean push went through. Lessons learned this session, in priority order:

1. **Clone to `/tmp/reelphone-work/` for any non-trivial git work**, not into the OneDrive mount. Mirrors the 998webdesigns Path B pattern.
2. If you must work in the OneDrive checkout, expect every `git` command to leave stale `.git/*.lock` files that bash cannot `rm` (OneDrive perms). Workaround: `mv` the lock file aside with a `.zold-$(date +%s)` suffix. `rm` is denied; `mv` is allowed.
3. After an Edit-tool write through Windows, bash will see stale `stat()` metadata for 5-30 seconds. `wc -c` and `md5sum` lie; `git status` misses the modification. Options: (a) wait for sync, (b) write the file directly from bash via heredoc, (c) `git update-index --really-refresh`.
4. **Watch for trailing null bytes.** A bash heredoc write earlier in this session injected 8 trailing `\0` bytes; required a follow-up commit (`tr -d '\0'`) to strip. Verify with `hexdump -C index.html | tail -3` after any sandbox-side file write. Browsers tolerate them but they trip grep, look unprofessional in diffs, and may confuse future tooling.
5. **PAT-embedded remote** for the duration of the push, then strip:
   ```bash
   PAT=$(cat /sessions/<session>/mnt/Claude/slatepress/.local/slatepress-demos-deploy-pat.txt)
   git remote set-url origin "https://x-access-token:${PAT}@github.com/bearllc555-spec/reelphone-com-site.git"
   git add index.html && git -c user.email="bearllc555@gmail.com" -c user.name="Anthony" commit -m "splash: ascii only message"
   git push origin main
   git remote set-url origin "https://github.com/bearllc555-spec/reelphone-com-site.git"
   ```
6. After push, mirror back to OneDrive only if the bash side has authoritative edits the Windows side doesn't. Otherwise let OneDrive sync from the next `git pull` in Cursor.

### What NOT to do

- Don't edit in OneDrive `repos/reelphone-com-site/` and then push from a `/tmp/reelphone-work/` checkout that's out of sync. Pick one cockpit per change.
- Don't bypass branch previews once an actual offer is on the splash. Today's "splash-only, no traffic" status means direct-to-main is tolerable for one-line copy tweaks; once the offer lands, treat reelphone like 998webdesigns and branch-first every change.
- Don't bake the PAT into the remote URL on the long-lived `repos/` clone. Strip after every push. `slatepress/CLEANUP-REMOTES.cmd` strips PATs if one slipped in.
- Don't use `rm` on `.git/` lock files in the OneDrive mount from bash. OneDrive denies it. Use `mv` aside instead.
- Don't push two commits within ~10 seconds. CF Pages dedups/drops the second one. The `v0X` label is the propagation tell — if the live site doesn't show your bumped version after ~60s, you got dropped, bump again and re-push.

---

## Brand & design rules (locked)

These are non-negotiable for reelphone:

1. **Zero Slatepress mention.** Not in copy, not in meta tags, not in JSON-LD, not in source comments. Reelphone stands on its own.
2. **Footer attribution is `a bear llc digital property` only.** No "another SlatePress company" line, no company-architecture explanation.
3. **Mercury-clean visual system:** white background (`#FFFFFF`), Inter font, ink-black text, blue (`#2563EB`) accent, lowercase H1, thin ink-black borders on cards/inputs, pulsing blue dot for any "live" / "in build" indicator.
4. **`v0X` version label next to brand mark.** Tiny slate-light text adjacent to `reelphone`. Bumps every commit. Diagnoses CF Pages propagation at a glance.
5. **Logo:** inline SVG, phone-with-play-button-and-sound-waves, `currentColor` stroke so it inherits ink-black. Stacked above the wordmark, ~6px gap, centered. ViewBox tightened to `0 22 240 196` to remove SVG slack — do not loosen it without re-tightening the spacing.
6. **Favicon:** the shared blue-slash mark used across slatepress / 998webdesigns. Don't invent a new convention.
7. **No CTA copy yet.** Resist the urge to add a mailto, signup form, or "notify me" hook until the offer is defined. The splash is a placeholder; treating it as a launched product invites premature commitments.

---

## Operational rules (inherit from slatepress)

These apply equally to reelphone and are documented exhaustively in `slatepress/CLAUDE.md`:

- **Branch previews mandatory** once the offer lands. Pre-offer (today), direct-to-main is tolerable for trivial fixes.
- **Code work in Cursor, non-code work in Cowork.**
- **One commit per change**, never two within ~10s.
- **ASCII-only commit messages** for any repo wired to `cloudflare/wrangler-action@v3`. No em-dashes, curly quotes, or Unicode sparkles. CF Pages deployments API rejects non-ASCII with `[code: 8000111] Invalid commit message UTF-8`.
- **`.local/` is sacred** — gitignored everywhere, holds all secrets, never committed.
- **OneDrive Files-On-Demand corruption gotcha** — see Path B notes above.
- **Pre-stage tabs FIRST, ask second.** If asking Anthony to do anything in the browser (CF dashboard, GitHub UI, DNS records), navigate Chrome to the exact page first.

---

## Recovery / cold restart

1. **Domain still resolves?** Check https://reelphone.com/. If 5xx: CF Pages dashboard. If DNS broken: re-create the apex CNAME (and add www if it was attached).
2. **Repo gone locally?** `git clone https://github.com/bearllc555-spec/reelphone-com-site repos/reelphone-com-site`. Or use `slatepress/CLONE-REPOS.cmd` if it has been updated to include this repo (verify before running).
3. **CF Pages project deleted?** Re-create at https://dash.cloudflare.com/e0f6f68f26f8a26a75eaa793385019ef/pages/new — name `reelphone-com`, connect GitHub repo `bearllc555-spec/reelphone-com-site`, branch `main`, build command empty, output dir `.`. Re-attach `reelphone.com` as a custom domain.
4. **Workflow secrets wiped?** Re-add `CLOUDFLARE_API_TOKEN` (from `slatepress/.local/cf-pages-token.txt`) and `CLOUDFLARE_ACCOUNT_ID` (`e0f6f68f26f8a26a75eaa793385019ef`) at https://github.com/bearllc555-spec/reelphone-com-site/settings/secrets/actions.
5. **PAT rotated?** Run `slatepress/UPDATE-PAT-AND-CLEAN.cmd`.
6. **Git index corrupt locally?** `mv .git/index .git/_index.corrupt-$(date +%s); git read-tree HEAD` rebuilds it from HEAD. (Encountered this session.)

---

## What's deferred / open

- **The offer itself.** Reelphone's product is undefined. Until it's locked, the splash is a placeholder. Suggestion: write `offer.md` next to this file once the offer crystallizes (mirror the 998webdesigns pattern — CLAUDE.md is ops, `offer.md` is product spec).
- **`www.reelphone.com` custom-domain attach.** Apex works; www is not wired. Add when the brand is ready for traffic.
- **Real `hello@reelphone.com` mailbox.** No mailto on the splash yet. Add when offer + CTA land.
- **Form-capture.** No form yet. If conversion needs measurement once an offer is live, use a NEW Formspree form (don't reuse the `xykonjqq` or `xjgjwobq` IDs from other brands — keep this brand's intake clean).
- **`offer.md` companion doc.** Sibling to this file once the product spec exists.
- **Schema.org JSON-LD + 1200x630 og:image.** Adds rich link previews on FB/LinkedIn/Twitter + Google knowledge panel. Quick polish win when traffic picks up.
- **CLONE-REPOS.cmd inclusion.** Verify whether `slatepress/CLONE-REPOS.cmd` currently re-clones reelphone-com-site on a cold restart, or whether it needs a one-line add.

---

## Session log

| Date | Event |
|---|---|
| 2026-05-12 | Repo created (`bearllc555-spec/reelphone-com-site`), initial coming-soon splash shipped (`846e6f5`). |
| 2026-05-12 | GitHub Actions workflow for Cloudflare Pages auto-deploy committed (`f971ca5`). |
| 2026-05-13 | Phone-with-play-button SVG logo added to splash (`0f98481`), tightened position and viewBox (`5bdabed`), stripped trailing null bytes from heredoc (`da7bfa0`). This handoff doc written and committed to repo root. |
