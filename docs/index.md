# What is Sage?

Sage helps you build and share prompt libraries with others. Whether you're working alone, with a team, or building a community — Sage handles versioning, collaboration, and distribution so you can focus on writing great prompts.

This document explains the ideas behind Sage: what problem it solves, how the pieces fit together, and why it works the way it does.

---

## The Problem: Prompts Decay

The best prompts degrade over time. Models change, tools change, edge cases accumulate, and "what worked" silently stops working. Most prompts live in private chat logs and copy-paste lore — no canonical version, no maintenance process, no way to know who changed what and why.

Sage exists because we believe the highest-value work isn't writing a prompt once — it's keeping a prompt aligned over time. That requires collaboration, governance, and incentives.

---

## Curated Skills for AI Agents

Sage is **agent-native**: agents discover and install skills on-the-fly during a session, using on-chain provenance to select trusted prompts.

The workflow is: **Discover → Install → Use → Iterate → Share**.

- **Discover** via search, trending feeds, or DAO membership
- **Install** through the CLI, MCP server, plugin system, or git clone
- **Use** locally inside the agent runtime
- **Iterate and Share** via versioned libraries and governance

The key difference from other skill systems: provenance is on-chain. You can verify who approved what, when, and under what governance. This matters for agents making autonomous decisions about which prompts to trust.

---

## What You Can Do

### Publish Your Work

Stop losing prompts in chat histories. Publish them to a library where you (and others) can find, use, and improve them. Every change is versioned and tracked. You can always roll back.

### Iterate With Others

The best prompts come from iteration. Share your library. Others try your prompts and suggest improvements. When someone submits a better version, review and merge it. Every contributor gets credit — the history shows who improved what.

### Build a Community

As your library grows, you might want others to help maintain it:

**Start with a team**: Invite 2-3 collaborators. Changes require approval from multiple people before going live.

**Open it up**: Let anyone propose improvements. Your community votes on which changes get adopted.

**Incentivize contributions**: Post bounties for specific improvements. Someone fixes the problem, they get paid.

---

## How the Pieces Fit Together

Sage is a protocol + toolchain. The protocol defines the trust boundaries (what is canonical, who can change it, and how). The toolchain makes the protocol usable for humans and agents.

**On-chain**: Smart contracts handle governance, ownership, and version pointers. DAOs decide what changes become canonical. Every decision is recorded.

**Off-chain**: IPFS stores the actual content (prompts, skills, manifests). A worker materializes the on-chain pointers into practical interfaces — search, git clone, marketplace feeds.

**Tooling**: The CLI, web app, SDK, and MCP server are the surfaces through which people and agents interact with the protocol. They abstract away the complexity of on-chain governance and content-addressed storage.

The organizing principle: **on-chain for trust, off-chain for content, tooling for usability**.

---

## Where to Go Next

- [Core Components](core-concepts/components.md) — The foundational pillars and how they connect
- [Governance Models](concepts/governance-models.md) — How different communities configure governance
- [Economic Thesis](concepts/economic-thesis.md) — Why prompts are governed capital
- [Bounties & Incentives](concepts/bounties.md) — How contributions are rewarded
- [Guides](guides/index.md) — Step-by-step instructions for common tasks
