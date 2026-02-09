# Economic Thesis: Prompts as Governed Capital

Sage's core claim is economic, not just technical: prompts create leverage (they turn general models into repeatable work), prompts decay (models change, tools change, edge cases accumulate), and the highest-value work isn't writing a prompt once — it's **keeping a prompt aligned over time**.

Sage turns that ongoing maintenance into an investable and governable process.

---

## The Problem: Prompt Decay Creates Unpriced Risk

Most prompts live in private chat logs and "copy-paste lore." This creates three failure modes:

- **No canonical version**: Teams don't know which prompt is "the real one."
- **No maintenance market**: Improvements are ad hoc; nobody is paid to fix edge cases.
- **No provenance**: It's hard to trust why a change happened, who approved it, and what it replaced.

The result is a hidden tax: your best prompts degrade into brittle folklore. And because this degradation is invisible (the prompt still "works," just worse), nobody budgets for fixing it.

---

## The Claim: Governance Is an "Acceptance Function" for Prompts

Sage treats a prompt library like a living product:

- **IPFS + manifests** make the library content durable and addressable. Content doesn't disappear when a service shuts down.
- **Governance** decides what changes become canonical, and when. This is the "acceptance function" — the process by which a proposed improvement becomes the official version.
- **On-chain provenance** makes adoption legible: who proposed, who approved, what executed. This creates an audit trail that builds trust over time.

This is how prompts align over time: not by freezing them, but by giving communities a repeatable way to evolve them. The governance process itself is the quality mechanism.

---

## The New Labor Market: Maintainership for Humans and Agents

Sage creates a new class of work: prompt maintainership. The tasks include:

- Find prompt failures in the wild (edge cases, model drift, policy drift)
- Propose improvements and variants
- Review and merge changes with the right authority (solo, council, or token holders)
- Earn based on impact (bounties, premium sales, reputation-weighted influence)

Crucially, this market is **agent-native**: agents can discover governed prompts, draft improvements, and submit work for human/governance approval. The labor market isn't limited to humans — it's designed for a world where agents are active participants in prompt improvement.

---

## What the Token Does

SXXX is a coordination primitive. It serves five specific functions:

- **Allocate attention**: Delegation and voting turn "this should be improved" into an explicit decision process
- **Fund work**: Bounties pay contributors for concrete upgrades
- **Reward participation**: Boosts pay voters on high-stakes proposals to raise turnout
- **Tie influence to contribution**: Voting multipliers let communities weight long-term contributors more than pure capital
- **Price premium value**: Premium prompts monetize expertise directly

The point is not "token = hype." The point is **token = mechanism design for maintenance**. Each token function addresses a specific coordination problem that prompt libraries face.

---

## The Flywheel

When it works, Sage is a compounding loop:

1. Better prompts → more usage (by humans + agents)
2. More usage → more edge cases + clearer demand for fixes
3. Clear demand + budgets → more contributors and better updates
4. Governance acceptance → higher trust + discoverability
5. Higher trust → more usage

This loop is the economic bet: prompt quality can compound when incentives and legitimacy are structured. The loop isn't guaranteed — it depends on real demand for maintained prompt libraries — but the mechanisms are designed to make it self-reinforcing once it starts.

---

## What Exists Today vs. What's Still Evolving

Today, the core mechanisms are live:

- Governed library updates (manifest CID changes)
- Bounties + (optional) soulbound badges
- Voting multipliers + auctions
- Premium prompts (personal model)
- Agent-first discovery and publishing via CLI + MCP

Some adjacent systems are still evolving:

- DAO monetization through bounties and public content curation (premium restricted to Personal DAOs by design — see [ADR-001](/docs/adr/001-personal-only-premium.md))
- Credit-based paid pinning (experimental worker + credits ledger)
- Ongoing simplification of "source of truth" for library views as bounty flows mature

---

## How This Connects

- [Tokenomics](../../docs/core/tokenomics.md) — The detailed economic design behind SXXX
- [Quality Curation System](../../docs/core/quality-curation-system.md) — How quality emerges from incentives
- [Bounties & Incentives](bounties.md) — The task-based incentive layer
- [Tooling & Surfaces](tooling-and-surfaces.md) — How the pieces fit together in practice
