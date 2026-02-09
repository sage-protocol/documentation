# Governance Models

This document explains the different governance models available in Sage Protocol, why we provide multiple options, and how communities should choose between them. Understanding these models helps you pick the right structure for your use case.

For step-by-step DAO creation, see [Creating a DAO](../guides/creating-a-subdao.md). For the playbook system and CLI commands, see [Governance Modes](../core-concepts/governance-modes.md).

---

## Why Multiple Models?

One size doesn't fit all governance. A solo creator needs instant control — voting delays would make iteration impossible. A small team needs consensus, but they trust each other — formal token voting would be unnecessary overhead. A large community needs democratic governance with safeguards — unilateral control would be unacceptable.

Rather than forcing everyone through the same process, Sage provides a **three-axis model** where each axis can be configured independently. The axes combine to create different governance patterns optimized for different community sizes and trust levels.

---

## The Three Axes

Sage governance is configured along three independent dimensions:

**Governance Kind** (OPERATOR vs TOKEN): Who decides? In operator mode, a trusted controller (wallet or multisig) makes decisions directly. In token mode, SXXX token holders vote on proposals. This is the most fundamental choice — it determines whether governance is hierarchical or democratic.

**Proposal Access** (COUNCIL_ONLY vs COMMUNITY_THRESHOLD): Who can propose changes? Council-only restricts proposal creation to a trusted set. Community threshold lets anyone with enough delegated tokens propose. This controls the "idea pipeline" into governance.

**Execution Access** (COUNCIL_ONLY vs ANYONE): Who can execute passed proposals? Council-only requires trusted operators to trigger execution. Anyone mode allows permissionless execution after timelock. This controls the "last mile" of governance — the actual state change.

These axes are orthogonal. You can mix and match: for example, community members propose, token holders vote, but only the council can execute. This combination gives voice to the community while maintaining execution control.

---

## Common Configurations

### Personal (Solo Creator)

OPERATOR + COUNCIL_ONLY + COUNCIL_ONLY

A single wallet controls everything. No voting required. Instant updates. This is the simplest and fastest governance mode.

We include this because solo creators are a primary audience. They need auditable, on-chain updates without governance overhead. All changes still flow through a Timelock for transparency — the creator can't silently modify the library.

### Council (Small Team)

OPERATOR + COUNCIL_ONLY + COUNCIL_ONLY (with Safe multisig)

A Safe multisig controls proposals and execution. Requires threshold signatures (e.g., 2-of-3). No token voting needed.

This is the natural governance for teams of 3-10 who trust each other but want mutual accountability. The multisig ensures no single member can make unilateral changes, while avoiding the overhead of community voting.

### Community (Token Democracy)

TOKEN + COMMUNITY_THRESHOLD + ANYONE

Anyone with enough tokens can propose. All token holders vote. Anyone can execute after the timelock. Full token democracy.

This is the target state for mature, decentralized DAOs. It's the most resistant to capture but also the slowest to iterate. We recommend starting simpler and transitioning to community governance as the DAO matures.

### Council Drafts (Hybrid)

TOKEN + COMMUNITY_THRESHOLD + COUNCIL_ONLY

Community proposes and votes. Council executes. This balances community voice with execution control.

We designed this specifically for the transition period between council and full community governance. The community gets meaningful input (their votes determine outcomes) while the council maintains a safety net (they can decline to execute malicious proposals that passed through voter apathy or manipulation).

---

## Choosing a Model

The right model depends on three factors:

**Community size**: Smaller communities have higher trust and can use simpler models. Larger communities need formal governance to prevent capture.

**Iteration speed**: Solo creators and small teams need to ship fast. Large communities can afford longer governance cycles because they have more collective context.

**Trust level**: High-trust environments (personal, small team) can delegate authority. Low-trust environments (open community) need democratic safeguards.

The general progression is: **personal → council → council-drafts → community**. Most DAOs should start simpler than they think they need and add complexity as the community grows and trust patterns emerge.

---

## Governance Parameters

Within each model, several parameters can be tuned:

**Voting period**: How long proposals are open. Fast (1-2 days) for small teams, standard (3-5 days) for most DAOs, long (7+ days) for major decisions. Longer periods increase accessibility but slow iteration.

**Quorum**: Minimum participation for a valid vote. Low quorum (1-2%) avoids deadlocks in large token supplies. High quorum (10%+) ensures broad consensus but risks governance paralysis.

**Proposal threshold**: SXXX required to create a proposal. See [Proposal Threshold](proposal-threshold.md) for details.

**Timelock delay**: Time between passing and execution. No delay for personal/operator. 1-2 days for teams. 2-7 days for communities. Delay provides a window for the community to react to passed proposals.

---

## Evolving Governance

DAOs can change their governance model over time through governance proposals. This is important because the right model at launch is almost never the right model at maturity.

The evolution path is always toward *more* governance, not less. You can add community voting to a council DAO, but you can't easily remove voting once the community expects it. This is by design — governance changes go through the existing governance process, which means the current stakeholders must approve any transition.

A common concern: "what if we want to go back?" The answer is forking. If a governance change doesn't work out, the community can fork the DAO with different parameters. This is a nuclear option, but it provides an escape valve that makes governance experimentation safer.

---

## How This Connects

- [Governance Modes](../core-concepts/governance-modes.md) — Playbook system and CLI commands
- [Creating a DAO](../guides/creating-a-subdao.md) — Step-by-step DAO creation with playbook selection
- [Proposal Threshold](proposal-threshold.md) — Anti-spam mechanism for proposals
- [Voting Multipliers](voting-multipliers.md) — Weighting contributor influence
- [Delegation & Voting Power](../guides/delegation-and-governance.md) — Getting voting power
