# Governance Modes

This document explains Sage Protocol's playbook system — a pre-configured set of governance modes that let communities start with the right governance model and evolve over time. It covers the 3-axis model, the available playbooks, and the design rationale behind them.

For the conceptual overview of governance models, see [Governance Models](../concepts/governance-models.md). For step-by-step DAO creation, see [Creating a DAO](../guides/creating-a-subdao.md). For governance parameter reference, see the [CLI Reference](../cli/command-reference.md).

---

## The 3-Axis Model

Every Sage DAO is configured along three independent axes:

**governanceKind** (OPERATOR vs TOKEN): Who decides — a trusted operator or token holders? This is the most fundamental choice, determining whether governance is hierarchical or democratic.

**proposalAccess** (COUNCIL_ONLY vs COMMUNITY_THRESHOLD): Who can create proposals? This controls the "idea pipeline" — whether proposals come from a trusted set or from anyone with enough tokens.

**executionAccess** (COUNCIL_ONLY vs ANYONE): Who can execute passed proposals? This controls the "last mile" — whether execution requires trusted operators or is permissionless after timelock.

The orthogonal design means you can mix and match. This is intentional: real communities don't fit neatly into "centralized" or "decentralized" — they're usually somewhere in between, and they evolve over time.

---

## The Playbooks

Playbooks are pre-configured combinations of the three axes, optimized for common scenarios. They exist because configuring three independent axes is overwhelming for new users. Playbooks provide opinionated defaults that match how most communities actually operate.

### Personal

OPERATOR + COUNCIL_ONLY + COUNCIL_ONLY

The fastest governance mode. Your wallet controls everything. No voting, no waiting. Every change still flows through a Timelock for auditability — you can't silently modify the library — but the process is immediate.

We include this because solo creators are a primary audience. Requiring community governance for a personal prompt library would be absurdly heavy. But we still want auditable, on-chain updates.

### Council-Closed

OPERATOR + COUNCIL_ONLY + COUNCIL_ONLY (with Safe multisig)

A Safe multisig controls the DAO. Only Safe signers can propose and execute. No external token voting. Good for small teams (3-10 people) with high mutual trust who want shared control.

The Safe integration means no single team member can make unilateral changes. The threshold mechanism (e.g., 2-of-3) provides accountability without the overhead of token voting.

### Council-Drafts

TOKEN + COMMUNITY_THRESHOLD + COUNCIL_ONLY

The community proposes and votes. The council executes. This is our answer to the "premature decentralization" problem — communities that want democratic input but aren't ready for fully permissionless execution.

The council acts as a safety net. If a malicious proposal passes through voter apathy or manipulation, the council can decline to execute it. This makes governance experimentation safer during the transition from centralized to decentralized control.

### Community

TOKEN + COMMUNITY_THRESHOLD + ANYONE

Full token democracy. Anyone with enough tokens can propose. Anyone can execute passed proposals after the timelock. This is the most resistant to capture but also the slowest.

We recommend this for mature DAOs with established trust patterns and active voter participation. Starting here before the community is ready often leads to governance paralysis (quorum not met) or capture (small groups dominate).

### Community-Long

Same as community but with extended voting periods (7 days) and lower quorum (2%). Designed for high-stakes decisions that need more deliberation time and broader accessibility.

---

## Token Delegation

For TOKEN governance modes, voting power comes from **delegated** SXXX tokens. This is a critical UX detail: holding tokens without delegating gives you zero voting power.

We use delegation-based voting (ERC20Votes) because it enables:

- **Passive participation**: Holders who don't want to vote can delegate to active community members
- **Checkpointed voting**: Historical snapshots prevent flash-loan governance attacks
- **Separation of holding and influence**: Token holders consciously choose to activate their governance power

The tradeoff is UX friction. "I hold tokens but can't vote" confuses new users. We mitigate this with CLI commands (`sage sxxx delegate-self`) and web app prompts, but it remains a common support question.

---

## Transitioning Between Modes

DAOs can evolve their governance over time. The typical progression is:

**Personal → Council**: As the creator builds a team, add a Safe multisig for shared control.

**Council → Council-Drafts**: As the community grows, open proposal access to token holders while maintaining council execution control.

**Council-Drafts → Community**: When the community has proven it can govern effectively, enable permissionless execution.

All transitions go through the existing governance process — you can't bypass voting to change voting rules. This means the current stakeholders must approve any transition, which ensures governance changes have legitimacy.

The reverse direction (community → council) is technically possible but socially difficult. Communities that have experienced democratic governance rarely accept returning to centralized control. If a governance change doesn't work out, forking is the more realistic escape valve.

---

## Tradeoffs

**Playbook simplicity vs. flexibility**: Playbooks are opinionated defaults. They make the common cases easy but may not cover every community's exact needs. Custom configurations are possible but require more expertise.

**Council-drafts as transition state**: This mode is designed for transitioning communities, but some communities may stay in this mode indefinitely if they never feel comfortable with permissionless execution. This is fine — it's a legitimate governance model, not just a waypoint.

**Delegation as activation**: Requiring explicit delegation adds friction but prevents "surprise governance" where passive holders suddenly vote. We believe the friction is worth the security, but it requires ongoing user education.

---

## How This Connects

- [Governance Models](../concepts/governance-models.md) — The conceptual framework for governance choices
- [Creating a DAO](../guides/creating-a-subdao.md) — Step-by-step DAO creation
- [Proposal Threshold](../concepts/proposal-threshold.md) — Anti-spam requirements
- [Delegation & Voting Power](../guides/delegation-and-governance.md) — Getting voting power
- [Governance (Architecture)](../../docs/core/governance.md) — How governance affects the codebase
