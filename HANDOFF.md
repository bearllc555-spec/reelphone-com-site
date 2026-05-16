# HANDOFF - 2026-05-15

Hello future-Claude. Anthony is starting a fresh chat after this one. Read this file alongside `CLAUDE.md` (which is the long-lived project context); this file is the session-specific delta you need to pick up where we stopped.

---

## Where we are right now

**Working branch:** `polish/icon-receive-reaction` (NOT `feature/redesign-02`).

**Why a new branch:** When this session started, the local checkout of `feature/redesign-02` had **7 stale commits** that diverged from the remote (`v16-v19` splash refinements + `cursor rules` + a `.gitignore` + a CI branch-config tweak). The remote `feature/redesign-02` was **53 commits ahead** with the entire hero redesign (v054-v065 - upload lines, orbit icons, pulse dots, the works). Rather than reset/destroy the 7 local commits, we branched off `origin/feature/redesign-02` into `polish/icon-receive-reaction` and worked from there. The 7 stale local commits are still on local `feature/redesign-02`, untouched.

**Latest deployed version:** **v071** at the preview URL:
- https://polish-icon-receive-reaction.reelphone-com.pages.dev/

**Production (`reelphone.com`) is untouched** - still serving the old simple splash (the version before the hero redesign even started). The full redesign hasn't been merged to `main` yet.

**Permission allowlist:** `.claude/settings.json` was written this session with broad allow rules so Anthony stops getting hammered with permission prompts. The file is **untracked by git** intentionally - it's developer-machine config and would otherwise deploy as a static asset on Cloudflare Pages (CF serves the repo root). Anthony said he'd restart Cursor / the Claude Code session to pick up the new settings.

---

## What we built this session (v066 -> v071)

The hero "phone broadcasting to social" animation already existed at v065. Anthony's iteration this session was about making the animation more storyteller-y: each packet arrival/departure should *feel* like a real event, with synced reactions.

| Ver | Commit | What changed |
|---|---|---|
| **v066** | `1b8799b` | When the green pulse dot arrives at each top icon (top 5 = Facebook/Instagram/TikTok/YouTube/X) or launches from each bottom icon (bottom 2 = Telegram/WhatsApp), the icon does a squash-and-stretch scale punch (1 -> 1.28 -> 0.92 -> 1.06 -> 1 with subtle rotation wobble) on the inner SVG **and** an emerald halo ring (`::before` pseudo, `border-radius:50%`, `inset:-4px`) expands outward + fades. Per-icon timing synced to each pulse-N animation-delay. Honors `prefers-reduced-motion`. |
| **v067** | `8e8e585` | Replaced the rotating 5s `label-cycle` (where each title flashed in sequence) with `label-on-arrival` keyframes - 2.5s cycle, peak opacity at 82-95%, delays matching pulse-N (2.0 / 2.3 / 2.6 / 2.9 / 3.2s). Result: each top icon's title text appears at the exact moment that icon's green dot lands. Telegram + WhatsApp unaffected (they were hover-only at this point). |
| **v068** | `2d97494` | First attempt to stagger bottom packets: orbit-7 delayed 0.3s behind orbit-6. **Superseded.** |
| **v069** | `88ccf39` | Second attempt: orbit-7 delayed 2s so it doesn't fire until orbit-6 lands at the phone. **Still wrong** - orbit-6 was looping at 2.5s, so orbit-7 only fired once before orbit-6 was already mid-flight again. **Superseded.** |
| **v070** | `0b7342c` | **Real ping-pong.** Created new `pulse-travel-long` keyframe (`stroke-dashoffset: 0 -> -200`) and applied it ONLY to `.upload-lines .pulse-6` and `.pulse-7` with `stroke-dasharray:0.001 200` and `animation-duration:4s`. With pathLength=100, the dot is visible for the first 50% of the 4s cycle (travel) and invisible the other 50% (rest). Result: pulse-6 fires at t=0/4/8/12..., pulse-7 fires at t=2/6/10/14... - neither launches until the other has landed. **icon-launch** and **halo-launch** keyframe percentages were re-tuned (6/14/22/30% -> 3.75/8.75/13.75/18.75% and 4/28% -> 2.5/17.5%) so the punch still peaks at ~0.15s and rests by ~0.75s regardless of the longer 4s cycle. Top 5 still on 2.5s; bottom 2 on 4s = polyrhythm with LCM=20s. |
| **v071** | `48348c7` | Telegram and WhatsApp labels now always visible below the phone. Replaced `.orbit-icon.orbit-6:hover::after, .orbit-icon.orbit-7:hover::after { opacity:1 }` with always-on rule, and added a mobile override (`display:block`) inside the `.orbit-icon[title]::after { display:none }` media-query block so they survive the mobile-label-hide. |

**Other artifacts this session:**

- `.claude/settings.json` - permission allowlist (created, untracked, not committed). Allows `git add/commit/push/fetch/pull/checkout/-c/-C/remote/stash/branch/merge/rebase/tag/cherry-pick/restore/reset`, `curl *`, `mkdir *`, `Edit/Write/Read/Grep/Glob *`, and the five `mcp__Claude_in_Chrome__*` tools (computer / find / form_input / navigate / get_page_text). Auto-allowed stuff (`ls`, `cat`, all git read-only, all gh read-only, etc.) is NOT in the list - they never prompt anyway.
- Memory file: `feedback_always_report_v_number.md` in `~/.claude/projects/.../memory/` - always lead status updates with the current v-number after a commit. Applies to all SlatePress portfolio sites with the v0X version-band convention.

---

## How the animation actually works (timing cheat-sheet)

**Top 5 (Facebook/Instagram/TikTok/YouTube/X):**
- Path: phone (50, 38) -> each icon position
- Pulse: 2.5s `pulse-travel` (dashoffset 0 -> -125, dasharray 0.001 125)
- Dot reaches icon at 80% of duration = 2.0s into the animation
- Pulse-N animation-delay: 2.0 / 2.3 / 2.6 / 2.9 / 3.2 seconds
- So first-cycle icon arrivals: t = 4.0 / 4.3 / 4.6 / 4.9 / 5.2 seconds
- Cycle repeats every 2.5s

**Bottom 2 (Telegram/WhatsApp):**
- Path: each icon position -> phone (50, 77.5)
- Pulse: 4s `pulse-travel-long` (dashoffset 0 -> -200, dasharray 0.001 200)
- Dot reaches phone at 50% of duration = 2.0s
- Pulse-6 animation-delay: 0s -> launches at t=0/4/8/12...
- Pulse-7 animation-delay: 2s -> launches at t=2/6/10/14...
- Phone arrivals: pulse-6 at 2/6/10/14, pulse-7 at 4/8/12/16

**Per-icon reactions:**
- Top icons: `icon-receive` (scale punch on inner svg, peaks at 82% of 2.5s cycle) + `halo-receive` (ring at 80%) + `label-on-arrival` (title text at 82-95%) - all sharing the same per-icon delay as that icon's pulse-N
- Bottom icons: `icon-launch` (peaks at 3.75% of 4s) + `halo-launch` (4% of 4s) - delays 0s / 2s

**Polyrhythm:** Top runs on 2.5s, bottom on 4s. They re-sync every 20 seconds (LCM). Anthony hasn't complained about this; if it ever bothers him, the fix is to move the top to 4s with the same `pulse-travel-long` keyframe.

---

## What's still open / to-do

**For the redesign branch (`feature/redesign-02`) overall:**

1. **Anthony reviews v071** at the preview URL. If approved, merge `polish/icon-receive-reaction` -> `feature/redesign-02` (a PR is already drafted at https://github.com/bearllc555-spec/reelphone-com-site/pull/new/polish/icon-receive-reaction).
2. **Decide what to do with the 7 stale local commits** on local `feature/redesign-02`. Inspecting them showed they're from an abandoned older splash track (v16-v19) that the redesign superseded. Recommended action when Anthony approves: `git checkout feature/redesign-02 && git reset --hard origin/feature/redesign-02`. **Destructive - do not do without explicit go-ahead.**
3. **Eventually merge `feature/redesign-02` -> `main`** to ship the full hero redesign + offer copy + everything to production at reelphone.com. This is a major launch, not a side task.

**Carried over from `CLAUDE.md`'s "What's deferred / open" section (still open):**

- `www.reelphone.com` custom-domain attach on the `reelphone-com` Pages project (apex works; www doesn't).
- Real `hello@reelphone.com` mailbox via Google Workspace domain alias.
- Form-capture - new Formspree form (don't reuse the `xykonjqq` or `xjgjwobq` IDs from other brands - keep reelphone's intake clean).
- `offer.md` companion doc sibling to `CLAUDE.md` (offer IS now defined: $1997 device+setup, $1700-$2700/mo, dedicated device + service for DTC/UGC, up to 90 UGC videos/mo - just hasn't been written down outside of `index.html`).
- Schema.org JSON-LD + 1200x630 og:image for rich social/search previews.
- Verify `slatepress/CLONE-REPOS.cmd` includes `reelphone-com-site` on a cold restart.

**Small loose ends from this session specifically:**

- The brace-count helper Anthony's been seeing me run (`opens=$(grep -o "{" index.html | wc -l); closes=$(grep -o "}" index.html | wc -l)`) still triggers a permission prompt because the leading token is the bash variable assignment `opens=$(grep`, not `grep`. The allowlist patterns can't easily cover this. **Workaround:** restructure the sanity check to start with `grep` directly (which is auto-allowed) - e.g. `grep -c "{" index.html` and `grep -c "}" index.html` separately. Don't use the bash-variable form.
- The `.claude/settings.json` is untracked. If Anthony wants belt-and-suspenders, add `.claude/` to a `.gitignore` so it can never accidentally deploy as a static asset on CF Pages. He hadn't done this before the new chat started; offer it as the first action of the new session if it matters.

---

## Critical rules to NOT break (from CLAUDE.md, restated because they matter)

- **ASCII-only commit messages.** This repo deploys via `cloudflare/wrangler-action@v3`. Em-dashes / curly quotes / Unicode in a commit message cause the deployment to fail at the deployments-API step with `[code: 8000111] Invalid commit message`. Use straight `-` and straight `'`/`"` only.
- **Branch previews mandatory.** Never push code changes directly to `main` on this repo. Feature branch -> CF Pages auto-builds at `<branch-slug>.reelphone-com.pages.dev` (slashes become dashes) -> review -> merge.
- **One commit per change, never two within ~10s.** CF Pages dedups the second one. The v0XX label is the propagation tell - if the live URL doesn't show your bumped version after ~60s, you got dropped, bump and re-push.
- **Lead status reports with the current v-number.** (Saved as a feedback memory.)
- **The `slatepress-demos-deploy` PAT** at `slatepress/.local/slatepress-demos-deploy-pat.txt` has Contents read+write across `bearllc555-spec` but does NOT have `Workflow write` - cannot edit `.github/workflows/*`. The workflow file is stable; never need to edit it via push. If you must edit it, use GitHub web UI.
- **Strip PAT from `.git/config`** after every push. The push commands embed it temporarily; always `git config branch.<name>.remote origin` immediately after.

---

## First action in the new chat

You probably want to start by:

1. Greeting Anthony briefly with the current state ("v071 is live at the preview URL, branch `polish/icon-receive-reaction`, ready for your review/next-direction").
2. If he wants to keep iterating on the hero animation -> read the timing cheat-sheet above and look at `index.html` lines ~258-440 (the orbit-icon CSS block).
3. If he wants to ship -> the merge sequence is `polish/icon-receive-reaction` -> `feature/redesign-02` -> `main`.
4. If he hits a permission prompt despite the new `settings.json` -> check `.claude/settings.json` and widen the allowlist.

Good luck out there.
