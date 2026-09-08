# PCB skill

An agent skill for taking a hardware idea all the way to **a board you can order, solder and bring up** —
concept, schematic, sourcing, layout, routing, verification, and a purchase staged to the pre-payment page.

It runs inside **Claude Code (desktop)** or **Codex (desktop)**, drives **EasyEDA Pro over MCP**, and does
its shopping in a browser you are already logged in to.

> This is not a tutorial about PCB theory. It is the set of gates that catch the things theory does not:
> a socket rotated so the display can never be plugged in, a capacitor under a module body, a keep-out that
> exists only on a drawing layer, a checker quietly measuring the previous release. Every rule here was paid
> for on a real board — see [`docs/case-study.md`](docs/case-study.md).

## The three principles

| | |
|---|---|
| **性价比** cost-effectiveness | every part priced from a live page or labelled an estimate; the BOM netted against stock you already own |
| **好装配** assemblable by hand | you are the assembler — no bottom-terminated packages, every pad reachable, a soldering order that does not trap a joint |
| **免费 PCB** a free prototype | design inside the free-prototype envelope from the first sketch, not at the end |

## Install

```bash
git clone https://github.com/daishuge/pcb-skill.git
cp -r pcb-skill/skills/pcb ~/.claude/skills/pcb        # Claude Code
# or: cp -r pcb-skill/skills/pcb ~/.codex/skills/pcb   # Codex
```

Then work through [`setup/README.md`](setup/README.md) — the skill checks these before it starts:

1. **EasyEDA Pro + the MCP bridge** — [`setup/easyeda-mcp.md`](setup/easyeda-mcp.md)
2. **A browser with the agent extension, logged in** to your PCB house and your parts suppliers —
   [`setup/browser-logins.md`](setup/browser-logins.md)
3. **An approval watcher**, if your desktop raises a permission card for every browser action —
   [`setup/auto-approve-macos.md`](setup/auto-approve-macos.md) ·
   [`setup/auto-approve-windows.md`](setup/auto-approve-windows.md).
   **Carts and payment stay manual on purpose** — the watcher never touches them.

## The workflow

| phase | produces | gate |
|---|---|---|
| 1 Concept | hard vs soft constraints, agreed | every question whose two answers give a different board has been asked |
| 2 Schematic + sourcing | schematic, priced BOM, staged carts | netlist assertions pass; every price VERIFIED or labelled ESTIMATE |
| 3 Layout + routing | placed, routed, poured board | placement is *routable*, not merely legal; DRC clean; adversarial review clear |
| 4 Fabrication + purchase | verified Gerber, order at the pre-payment page | the package is verified independently of the tool that made it |

**The agent never places an order and never pays.** It drives to the pre-payment page and hands you the click.

## What is in here

```
skills/pcb/SKILL.md          the skill itself
skills/pcb/references/       loaded on demand: workflow, review protocol, manufacturing,
                             sourcing, EDA-MCP traps, mechanical/3D, bring-up
scripts/placement/           courtyards from real pads, via lanes, module-body clearance
scripts/routing/             Specctra DSN/SES autoroute chain, four-state watchdog, rate floor
scripts/verify/              Gerber parsing, clearance sweeps, drill census, netlist assertions,
                             3D interference — deliberately independent of the EDA
scripts/notify/              a progress relay, so a long run is never silent
setup/                       getting the environment ready
docs/case-study.md           one board, the defects the process caught, and the honest cost
```

## Four rules that did the work

- **A green board is not a correct board.** Every phase ends with a one-pass, read-only adversarial review
  that re-derives the numbers instead of reading them — and its termination criterion is written *before*
  it starts, or it loops.
- **Measured, not inferred.** "This probably is not supported" is not a finding. If a claim decides whether
  work continues, prove it, and say which numbers were measured and which computed.
- **Verify the tool before the result.** Three checkers on the reference project silently measured the wrong
  thing. Calibrate every geometric transform against a known asymmetric object.
- **A wait condition must answer:** if this crashed right now, would my filter emit a line? One run on the
  reference project sat silent for 10 h 53 m because the answer was no.

## Licence

MIT — see [`LICENSE`](LICENSE).

## Star history

<a href="https://star-history.com/#daishuge/pcb-skill&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=daishuge/pcb-skill&type=Date&theme=dark" />
    <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=daishuge/pcb-skill&type=Date" />
    <img alt="Star history chart for daishuge/pcb-skill" src="https://api.star-history.com/svg?repos=daishuge/pcb-skill&type=Date" />
  </picture>
</a>

## 友情链接

- [linux.do](https://linux.do)
