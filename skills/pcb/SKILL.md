---
name: pcb
description: Design a manufacturable PCB end to end — concept, schematic, layout, routing, verification, and a staged purchase — driven from Claude Code or Codex on the desktop, with EasyEDA Pro over MCP and JLC/LCSC/Taobao in a logged-in browser. Optimises for cost-effectiveness, hand-assembly, and a free prototype board.
---

# PCB

Take a hardware idea to a board a human can actually order, solder and bring up.

This skill is written from a real project that went from a blank page to a released
4-layer board. Most of what is here is not PCB theory — it is the specific set of
failures that theory does not catch, and the gates that catch them.

## The three principles this skill optimises for

1. **性价比 — cost-effectiveness.** Every part is chosen on measured price and real
   availability, netted against stock the user already owns. A price nobody has read off
   a live page is an ESTIMATE and must be labelled as one.
2. **好装配 — assemblable by hand.** The user solders this. No QFN/DFN/exposed-pad-only
   packages unless the user says otherwise. Every pad reachable with an iron, in an order
   that does not trap a joint behind a tall part.
3. **免费 PCB — the board should cost nothing.** JLC's free-prototype coupon has hard
   geometric and process limits. Design inside them from the first sketch, not at the end.

## Before you start — the environment must be ready

Do not begin design work until these are true. Check, do not assume.

- **EasyEDA Pro** running, with the MCP bridge installed and answering (`server_info`).
  See `setup/easyeda-mcp.md`.
- **A browser with the agent extension**, logged in to the sites the project needs:
  Taobao, JD, JLC/LCSC. See `setup/browser-logins.md`.
- **An approval watcher** if the platform pops permission cards for every browser action,
  or the agent blocks on a card it cannot press itself. See `setup/auto-approve-*.md`.
  **Carts and payment stay manual** — the watcher is deliberately bypassed there.
- A **notification channel** for long runs (`scripts/notify/`), so silence is never
  ambiguous.

## The workflow — four phases, each with a gate

Read `references/01-workflow.md` for the full version. In outline:

| phase | produces | gate before moving on |
|---|---|---|
| **1 Concept** | what it does, for whom, the hard vs soft constraints | the user has answered every question whose two answers give different boards |
| **2 Schematic + sourcing** | full schematic, priced BOM, staged carts | netlist assertions pass; every price VERIFIED or labelled ESTIMATE |
| **3 Layout + routing** | placed, routed, poured board | placement is *routable*, not merely legal; DRC 0/0; adversarial review clear |
| **4 Fabrication + purchase** | verified Gerber, order at the pre-payment page | the package is verified independently of the EDA that produced it |

**Never place an order or pay.** Drive to the pre-payment page and stop.

## The rules that actually caught the bugs

These are load-bearing. `references/02-review.md` explains each with the failure it caught.

- **A green board is not a correct board.** Every phase ends with a one-pass, read-only,
  adversarial review by a fresh reviewer that re-derives the numbers instead of reading
  them. Write its termination criterion *before* it starts, or it will loop.
- **Measured, not inferred.** "X probably does not support Y" is not a finding. If a claim
  decides whether work continues, prove it. Say which numbers were measured and which
  were computed.
- **A wait condition must answer: if this crashed right now, would my filter emit a line?**
  Watchdogs cover four terminal states — output present, crash signature, process gone,
  wall-clock timeout — plus a progress-rate floor.
- **Verify the tool before trusting the result.** Three separate checkers on this project
  silently measured the wrong thing (a cached 3D model, a bounding box instead of a pad, a
  merged copper label). Calibrate every geometric transform against a known asymmetric object.
- **The last honest step is the user's.** Logins, CAPTCHAs, and payment are theirs. Prepare
  everything else so their part is two minutes.

## References — load what the current phase needs

| file | when |
|---|---|
| `references/01-workflow.md` | always, at the start |
| `references/02-review.md` | before every review, and when writing any checker |
| `references/03-jlc-manufacturing.md` | before fixing the board outline, stack-up or rules |
| `references/04-sourcing.md` | choosing parts, pricing, netting against stock, staging carts |
| `references/05-easyeda-mcp.md` | before the first write to EasyEDA — it is a trap list |
| `references/06-mechanical.md` | before declaring a board assemblable; 3D, connectors, folds |
| `references/07-bringup.md` | flashing and first power-up, especially with no USB-serial adapter |

## Scripts

`scripts/` holds the checkers this workflow depends on. They are deliberately independent
of the EDA: they parse exported documents and Gerbers, so they can contradict the tool that
produced them. Each directory has its own README.

- `scripts/placement/` — courtyards from real pads, via-lane widths, module-body clearance
- `scripts/routing/` — the DSN/SES autoroute chain with a four-state watchdog and a rate floor
- `scripts/verify/` — netlist assertions, Gerber parsing, drill census, 3D interference
- `scripts/notify/` — a progress relay so a long run is never silent
