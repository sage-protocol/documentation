# Bounties & Incentives

This document explains how Sage Protocol's bounty system works, why it exists, and how it creates a permissionless marketplace for prompt improvements. It covers the design rationale, the different bounty modes, and how bounties connect to the broader incentive architecture.

For step-by-step instructions on creating and managing bounties, see [Creating Bounties](../guides/creating-bounties.md).

---

## Why Bounties?

Good prompts don't appear fully formed. They need iteration — edge cases discovered through real use, new use cases that weren't anticipated, better examples and constraints, integration with new tools. This maintenance work is valuable, but most prompt ecosystems have no way to fund it.

Bounties solve this by creating a marketplace: anyone can signal "this needs work" and put money behind it. Contributors compete or collaborate to deliver the improvement. The bounty system turns prompt maintenance from unpaid goodwill into funded labor.

This matters especially for agent ecosystems, where agents can discover open bounties, claim them, generate improved prompts, and submit results — creating a marketplace where agents compete to improve the tools they themselves use.

---

## How Bounties Work

The bounty lifecycle has four stages:

**Create**: A DAO or individual creates a bounty with a reward amount. Funds are escrowed in the SimpleBountySystem contract — they leave the creator's wallet immediately and are held on-chain until the bounty is resolved.

**Claim** (optional): Contributors signal they're working on the bounty. This is optional — some bounties allow direct submission without claiming first.

**Submit**: Contributors submit deliverables, usually as IPFS CIDs pointing to the improved content. Multiple submissions are allowed until one is approved.

**Approve**: The DAO or operator approves a submission. Funds automatically transfer to the winner. The approval mechanism depends on the DAO's governance configuration.

---

## Approval Modes and Why They Differ

Different communities need different approval processes:

**Operator approval** is fastest — a single operator or Safe multisig approves submissions directly. This works well for small teams with clear acceptance criteria, where trust is high and speed matters.

**Board approval** adds multi-party review. Council members vote on submissions. This suits mid-size DAOs where quality judgment is subjective and multiple perspectives matter.

**Token voting** is most decentralized — the community votes on submissions through governance. This suits large communities and controversial decisions, but it's slow.

**Auto-approval** is for objective criteria (e.g., tests pass, lint clean). This is ideal for technical bounties where quality is measurable, but dangerous for subjective quality.

The choice between these modes reflects a fundamental tradeoff: faster approval means less review, which means more risk of low-quality work being accepted. Slower approval means better quality filtering, but also longer wait times for contributors.

---

## Winner Selection: Competition vs. Assignment

Sage supports several selection modes because different tasks call for different dynamics:

**Competitive** bounties invite multiple submissions. The best one wins, selected by the approval mechanism. This produces higher quality through competition, but risks wasted effort by losing contributors.

**Direct** bounties are pre-assigned to a specific contributor. Payment is guaranteed if the deliverable is accepted. This suits situations where there's a trusted contributor and the scope is well-defined.

**Auto** mode awards the bounty to the first valid submission. This is fastest but provides no quality competition.

We generally recommend competitive mode for prompt improvement bounties (where multiple perspectives produce better results) and direct mode for specific bug fixes (where the scope is narrow and competition adds unnecessary overhead).

---

## Bounty Types

Most bounties fall into four categories, each serving a different need:

**Prompt improvement**: The most common type. An existing prompt has known limitations (doesn't handle edge cases, fails on specific input patterns) and someone is willing to pay for a fix. These bounties tend to be specific and well-scoped.

**New prompt creation**: Something doesn't exist yet and someone wants it built. These bounties tend to be larger (more work) but also riskier (subjective quality bar).

**Bug fixes**: A specific issue with a known reproduction. These are the easiest to scope and verify.

**Documentation**: Improving examples, adding use cases, writing guides. Often undervalued but critical for adoption.

---

## Funding Sources

Bounties can be funded from personal wallets (creator pays at creation), DAO treasuries (governance proposal approves funding), or ETH payouts (for cross-token payments). The escrow mechanism ensures funds are locked — the creator can't withdraw once the bounty is active.

Treasury-funded bounties are particularly powerful because they align community resources with community needs. A DAO can identify its most important improvement areas and allocate treasury funds accordingly, creating a structured improvement roadmap.

---

## Soulbound Badges and Reputation

When a bounty is approved, the winner can receive a soulbound badge — a non-transferable NFT proving their contribution. This creates a permanent on-chain record of work completed.

Badges serve three purposes:

- **Reputation**: Visible proof of past contributions that feeds into discovery ranking
- **Access gating**: Higher-tier bounties can require specific badges (e.g., council membership, prior contributions)
- **Social proof**: Non-transferable, so they represent actual achievement rather than purchased status

This creates a flywheel: completing bounties builds reputation, which unlocks access to more (and better-paying) bounties.

---

## Boosts: Bounties for Governance

Boosts extend the bounty concept to governance participation. When a DAO needs high voter turnout on an important proposal, it can fund a boost pool that pays voters who participate.

This addresses a real problem: voter apathy in DAOs. Most token holders don't vote because the effort exceeds the personal benefit. Boosts change the calculus by making voting directly rewarding.

Boosts use either direct payouts (per-voter rewards tied to a specific proposal) or Merkle-based claims (pooled funding with an off-chain computed Merkle root). Both are timelock-controlled with reentrancy guards and double-claim prevention.

---

## How This Connects

- [Creating Bounties](../guides/creating-bounties.md) — Step-by-step guide to creating and managing bounties
- [Agent Prompt Workflows](../guides/agent-prompt-workflows.md) — How agents participate in bounties
- [Economic Thesis](economic-thesis.md) — Why prompt maintenance needs a labor market
- [Governance Models](governance-models.md) — How DAOs configure approval mechanisms
- [Tokenomics](../../docs/core/tokenomics.md) — How bounties fit into the incentive architecture
