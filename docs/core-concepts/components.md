# Core Components

This document explains the three foundational pillars of Sage Protocol — on-chain governance, persistent storage, and discovery tooling — and how they work together to enable governed, distributed prompt libraries.

---

## Why Three Pillars?

Sage could have been built as a centralized service: upload prompts, manage access, done. We chose a decentralized architecture because prompt libraries have specific properties that centralization handles poorly:

- **Prompts need versioning with provenance** — not just "what changed" but "who approved the change and why." On-chain governance provides this.
- **Prompts need durable storage** — content that doesn't disappear when a company shuts down. Content-addressed IPFS storage provides this.
- **Prompts need efficient discovery** — fast search, trending, and machine-readable interfaces. Off-chain indexing and tooling provide this.

No single technology handles all three well. The three-pillar architecture separates concerns: on-chain for trust, off-chain for content, tooling for usability.

---

## On-chain Governance

Every Sage DAO consists of interconnected smart contracts that manage proposals, voting, and execution:

- **SubDAO Factory** deploys new DAOs with pre-configured governance playbooks
- **Governor** manages the proposal lifecycle (create, vote, queue, execute)
- **Timelock** enforces delays between approval and execution for security
- **LibraryRegistry** tracks which manifest CID is the "current" version for each DAO
- **Treasury** holds DAO funds, controlled by governance

The key design principle: governance determines what "latest" means. When a proposal passes and executes, the LibraryRegistry updates its pointer, and that update cascades through the rest of the system (subgraph indexes it, worker caches it, discovery surfaces it).

This means all library changes are auditable. You can trace any version to the proposal that introduced it, the vote that approved it, and the execution that made it live. This provenance chain is what makes governed prompts trustworthy — not a central authority's stamp of approval, but a verifiable decision process.

---

## Persistent Storage (IPFS)

IPFS provides immutable, content-addressed storage for prompt libraries. "Content-addressed" means the same content always produces the same CID — if the content changes, the CID changes. This has three important implications:

**Deduplication**: The same prompt stored by different people produces the same CID. There's no wasted storage for identical content.

**Tamper detection**: If someone modifies a prompt, its CID changes. Agents can verify that what they received matches the CID that governance approved.

**Verification**: The CID on-chain can be compared to the CID of the content you received. If they match, you have the governed version. If they don't, something is wrong.

Manifests are the unit of library state. A manifest is a JSON file listing all prompts in a library with their CIDs. When governance updates a library, it's updating the manifest CID — the single pointer that describes the entire library state.

---

## Discovery & Tooling

Multiple interfaces connect users and agents to governed knowledge:

**The CLI** is the primary interface for creators and operators. It's the most complete workflow surface — publishing, governance, installs, local project setup — and it scripts well for automation.

**The web app** provides a browser-based interface for discovering libraries, voting on proposals, and managing DAOs. It lowers the barrier for non-CLI users.

**The MCP server** provides Model Context Protocol endpoints for AI agents. Agents can search libraries, fetch prompts, and discover DAOs programmatically.

**The subgraph** provides a GraphQL API for efficient querying. All the other surfaces use the subgraph for fast reads, falling back to direct RPC when needed.

These surfaces exist because different audiences need different interfaces to the same underlying protocol. The CLI is for power users, the web app is for casual users, the MCP server is for agents, and the subgraph is for developers building custom integrations.

---

## How Components Connect

The connection pattern is consistent:

1. **Creator** uses CLI to upload prompts to IPFS and create governance proposals
2. **Governor** manages voting; **Timelock** enforces delay
3. **LibraryRegistry** stores the approved manifest CID on-chain
4. **Subgraph** indexes the update event
5. **Worker** caches the indexed data for fast access
6. **Agents/Apps** query the worker and fetch content from IPFS

The important distinction: on-chain components (Governor, Timelock, LibraryRegistry) provide trust and provenance. Off-chain components (subgraph, worker, CLI) provide speed and usability. If any off-chain component fails, the on-chain state is unaffected — you can always reconstruct the truth from the chain.

---

## Key Design Principles

### Content Addressing

Every piece of content has a unique CID derived from its contents. This means agents can verify they received the governed version without trusting any intermediary. The verification is mathematical, not institutional.

### Governance-First

All changes to libraries go through governance — even in personal DAOs where the creator is the sole voter. This creates an audit trail for every change. The governance process is the quality mechanism, not a central review team.

### Separation of Concerns

On-chain handles governance, ownership, and version pointers. Off-chain handles content storage and indexing. Tooling handles user and agent interfaces. This separation keeps gas costs low (only pointers on-chain) while maintaining security and verifiability.

---

## How This Connects

- [Governance Modes](governance-modes.md) — How different DAOs configure governance
- [Governance Models](../concepts/governance-models.md) — Why multiple governance models exist
- [Architecture](../../docs/core/architecture.md) — Technical architecture details
- [Economic Thesis](../concepts/economic-thesis.md) — Why governed prompts need this architecture
