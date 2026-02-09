# Voting Multipliers

This document explains Sage Protocol's voting multiplier system — what problem it solves, how it works, and the design tradeoffs involved in letting DAOs weight contributor influence beyond pure token holdings.

For step-by-step instructions on creating multiplier tiers and checking your voting power, see [Creating a DAO](../guides/creating-a-subdao.md) and [Delegation & Voting Power](../guides/delegation-and-governance.md).

---

## The Problem: Capital ≠ Contribution

Token-weighted voting has a well-known flaw: someone who bought tokens yesterday has the same voting power as someone who has been building the DAO for a year. Pure capital-weighted governance creates a system where financial resources, not actual contribution, determine influence.

This misalignment matters for prompt library governance. The people who write, review, and maintain prompts develop domain expertise that makes their quality judgments more valuable than those of passive token holders. A system that treats all token holders identically ignores this expertise gradient.

---

## How Multipliers Work

Multipliers are ERC-721 NFTs with associated multiplier values. If you hold a "Founder" NFT with a 2x multiplier and have 1,000 delegated SXXX tokens, your effective voting power is 2,000.

Multiple multipliers stack multiplicatively: if you also hold a "Contributor" NFT (1.5x), your effective votes become 1,000 × 2 × 1.5 = 3,000.

This is intentionally simple — more complex schemes (quadratic multipliers, decaying multipliers, conditional multipliers) were considered and rejected because they create voter confusion and are harder to reason about during governance decisions.

---

## Key Design Properties

### DAO-Scoped

Each multiplier NFT belongs to a specific DAO. A "Founder" NFT from DAO A has no effect on voting in DAO B. We chose this over global multipliers because:

- It prevents whales from buying multipliers and dominating multiple communities
- It keeps each DAO's governance self-contained
- It lets different communities have different multiplier schemes without global coordination

### Transferable (Unlike Badges)

Unlike soulbound badges, multiplier NFTs can be sold or transferred. The multiplier follows the NFT, not the person. This was a deliberate choice:

- It creates a price signal: if a DAO's multipliers are valuable, people will pay for them, which reflects community health
- It allows founders to exit gracefully without abandoning governance influence
- It enables secondary markets that fund DAO treasuries

The counterargument is that transferability dilutes the "earned through contribution" intent. We mitigate this by also having non-transferable soulbound badges for pure reputation.

### Checkpointed

Multipliers are recorded at every transfer to prevent governance gaming. You can't buy an NFT after a proposal is created and vote with it — historical snapshots use the multiplier you had at proposal creation time. This prevents flash-loan attacks on governance and last-minute multiplier purchases before important votes.

### Capped

Maximum 5x multiplier per account, maximum 20 NFTs per account. These caps prevent extreme concentration where a single holder could override an entire community's vote. The specific values (5x, 20 NFTs) were chosen as pragmatic bounds — high enough to meaningfully reward top contributors, low enough to prevent governance capture.

---

## The Tier System

DAOs organize multipliers into tiers — categories of NFTs with different multiplier values, supplies, and distribution methods:

**Founding Member** tiers (typically 2-3x) are airdropped to early supporters. They recognize the risk early participants took when the DAO had no proven value.

**Contributor** tiers (typically 1.5-2x) are earned through bounty completion and governance participation. They reward actual work.

**Supporter** tiers (typically 1.25-1.5x) can be publicly minted or auctioned. They let the broader community buy governance influence while funding the DAO treasury.

**Auction** tiers use Nouns-style continuous auctions — a new NFT is minted and auctioned daily, with proceeds going to the DAO treasury. This provides ongoing treasury funding and continuous onboarding of new governance participants.

---

## When to Use (and Not Use) Multipliers

Multipliers are a tool, not a requirement. They're most valuable when:

- **Recognizing founders** who took early risk and built the foundation
- **Rewarding expertise** — contributors who earn lasting influence, not just token payments
- **Funding treasuries** through auction-based distribution
- **Weighting quality judgment** — domain experts whose quality assessments are more valuable than average

Skip multipliers when:
- **Pure token democracy** is the explicit goal
- **Simplicity** is paramount (small DAOs with < 10 members)
- **Avoiding privileged voters** is a community value

---

## Tradeoffs

**Complexity vs. fairness**: Multipliers add governance complexity (more parameters, more edge cases) but provide fairer representation of actual contribution. We believe this tradeoff is worthwhile for DAOs that care about quality governance.

**Transferability vs. meritocracy**: Transferable multipliers can be bought, which undermines the "earned through contribution" signal. We mitigate this with soulbound badges for pure reputation and caps on multiplier stacking.

**Checkpointing cost**: Recording multipliers at every transfer adds gas cost to NFT transfers. This is modest on Base L2 but would be expensive on mainnet Ethereum. Base's low gas costs make this design viable.

---

## How This Connects

- [Governance Models](governance-models.md) — How multipliers fit into the broader governance system
- [Delegation & Voting Power](../guides/delegation-and-governance.md) — Getting and managing voting power
- [Proposal Threshold](proposal-threshold.md) — How multipliers interact with proposal eligibility
- [Creating a DAO](../guides/creating-a-subdao.md) — Setting up multipliers during DAO creation
