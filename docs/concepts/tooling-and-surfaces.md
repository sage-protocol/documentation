# Tooling & Surfaces

Sage is best understood as a **protocol + toolchain**: the protocol defines the trust boundaries (what is canonical, who can change it, and how), and the toolchain makes the protocol usable for humans and agents. This page explains why each surface exists and how they work together.

---

## Why So Many Surfaces?

A common reaction to Sage's architecture is "why are there so many packages?" The answer is that the protocol serves fundamentally different audiences:

- **Contracts** are for trust guarantees (on-chain governance, ownership, provenance)
- **IPFS** is for durable content storage (not owned by any platform)
- **The worker** is for practical access (search, installs, marketplace feeds)
- **The CLI** is for operators and agents who need scriptable workflows
- **The web app** is for humans who prefer a browser
- **The SDK** reduces drift between all these surfaces

Each surface exists because its audience has different needs. Removing any one would make the protocol unusable for a significant user group.

---

## How the Surfaces Connect

At a high level, Sage's "content upgrade" loop:

1. **Authoring**: Prompts are created or edited locally (humans or agents)
2. **Packaging**: A manifest describes a versioned library
3. **Storage**: Content is pinned to IPFS (direct provider or worker)
4. **Governance**: Proposals decide which manifest becomes canonical
5. **Indexing**: Subgraph + discovery surfaces make the canonical version easy to find
6. **Consumption**: Agents and apps pull governed prompts by DAO/library context

The important framing: **off-chain tooling accelerates iteration; on-chain governance preserves legitimacy**. Both are necessary. Governance without tooling is unusable. Tooling without governance is untrustworthy.

---

## ERC-8004 + A2A: The Agent Discovery Rail

ERC-8004 agent cards and A2A JSON-RPC are the discovery/consumption rails for autonomous agents. The agent card advertises a prompt library resource; A2A is the paid convenience surface that returns trust-ranked, personalized results.

This exists because agents have different discovery needs than humans. Humans can browse and evaluate visually. Agents need structured, ranked results they can consume programmatically. A2A provides this — and the payment mechanism ensures the ranking stays honest (you pay for quality, not just quantity).

---

## Why This Matters for the Economic Thesis

If Sage's bet is "prompts can compound when maintained," then the tooling must make three things cheap:

- **Making changes** (iteration speed) — the CLI and MCP server
- **Approving changes** (legitimacy) — governance contracts and the web app
- **Finding the best version** (distribution) — the worker, subgraph, and A2A

The packages exist to make this loop viable for a new class of labor (prompt maintainers and agents). If any leg of the loop is too expensive or too slow, the flywheel doesn't spin.

---

## Architectural Tradeoffs (Honest Critique)

Sage makes several pragmatic choices worth stating explicitly:

**Dependence on off-chain indexing.** The subgraph is not a trust anchor, but it is a UX dependency. This is a reasonable trade when the alternative is RPC scanning, but it raises availability and consistency concerns for production apps. If the subgraph is down, governance UX and discovery degrade.

**An IPFS "front door" is partially centralized.** The worker improves reliability (pinning, cache warming, optional paid storage), but it concentrates operational risk. Content addressing limits the blast radius (the CID is still the truth), yet availability becomes partly an ops problem.

**Manifest updates are atomic but coarse.** Treating the manifest as the library's unit of change makes upgrades clean and auditable, but it means "small edits" still republish a manifest, and concurrent updates can clobber each other without a compare-and-set guard.

**Multiple governance models reduce friction but increase complexity.** The operator → council → community gradient matches how communities evolve, but it creates more surface area for tooling and more ways for users to misunderstand authority.

These tradeoffs aren't fatal — they are the cost of shipping a usable, agent-first protocol. The key is to keep pushing complexity down into shared libraries (SDK/CLI/MCP) while keeping the on-chain trust anchor small and legible.

---

## How This Connects

- [Architecture](../../docs/core/architecture.md) — The technical architecture in detail
- [Economic Thesis](economic-thesis.md) — Why the toolchain matters for the economic model
- [REPO_MAP](../../docs/architecture/REPO_MAP.md) — How the packages are wired together
- [Worker](../../docs/core/worker.md) — The materialization layer
- [Subgraph](../../docs/core/subgraph.md) — The indexing layer
