# HANDOFF - reelphone.com hero redesign launch

**Status: SHIPPED 2026-05-15.** Full hero redesign live at https://reelphone.com/ at v071.

The launch sequence: polish/icon-receive-reaction was force-pushed onto main (replacing the abandoned v15-v19 splash track), then polish + feature/redesign-02 + feature/offer-hero were deleted. Main is the only branch. Linear history.

This file is a frozen reference for the animation work that landed. The session-bridge intent it was originally written for is satisfied. Read `CLAUDE.md` for ongoing project context.

---

## Animation timing cheat-sheet (durable)

The hero "phone broadcasting to social" animation: top 5 icons (Facebook/Instagram/TikTok/YouTube/X) receive packets from the phone; bottom 2 icons (Telegram/WhatsApp) send packets to the phone. Per-icon scale punch + emerald halo ring at the moment the pulse dot arrives.

**Top 5 (phone -> icon):**
- Path: phone (50, 38) -> each icon position
- Pulse: 2.5s `pulse-travel` (dashoffset 0 -> -125, dasharray 0.001 125)
- Dot reaches icon at 80% of duration = 2.0s into the animation
- Pulse-N animation-delay: 2.0 / 2.3 / 2.6 / 2.9 / 3.2 seconds
- First-cycle arrivals: t = 4.0 / 4.3 / 4.6 / 4.9 / 5.2 seconds
- Cycle repeats every 2.5s

**Bottom 2 (icon -> phone), ping-pong:**
- Path: each icon position -> phone (50, 77.5)
- Pulse: 4s `pulse-travel-long` (dashoffset 0 -> -200, dasharray 0.001 200)
- Dot reaches phone at 50% of duration = 2.0s
- Pulse-6 (Telegram) delay 0s -> launches at t = 0 / 4 / 8 / 12 ...
- Pulse-7 (WhatsApp) delay 2s -> launches at t = 2 / 6 / 10 / 14 ...
- Neither launches until the other has landed at the phone.

**Per-icon reactions:**
- Top icons: `icon-receive` (scale punch on inner svg, peaks at 82% of 2.5s cycle) + `halo-receive` (ring at 80%) + `label-on-arrival` (title text at 82-95%) — all sharing the same per-icon delay as that icon's pulse-N
- Bottom icons: `icon-launch` (peaks at 3.75% of 4s) + `halo-launch` (4% of 4s) — delays 0s / 2s

**Polyrhythm:** Top runs on 2.5s, bottom on 4s. They re-sync every 20 seconds (LCM). If it ever bothers anyone, move the top to 4s with the same `pulse-travel-long` keyframe.

**Reduced-motion:** All scale/halo/label animations honor `prefers-reduced-motion`.

CSS for the orbit-icon block is roughly at `index.html` lines 258-440.

---

## Ship-loop reminder (carried over from CLAUDE.md)

- **Branch previews mandatory.** Never push directly to main now that production is serving the redesign. Feature branch -> CF Pages preview at `<branch-slug>.reelphone-com.pages.dev` -> review -> merge.
- **ASCII-only commit messages.** This repo deploys via `cloudflare/wrangler-action@v3`; non-ASCII fails at the deployments API.
- **One commit per change, never two within ~10s.** CF Pages dedups the second. v0XX label is the propagation tell.
- **Lead status reports with the current v-number.**
