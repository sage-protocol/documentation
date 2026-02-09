# Proposal Threshold

This document explains Sage Protocol's proposal threshold mechanism — why it exists, how it interacts with other governance parameters, and the design decisions behind its behavior.

For CLI commands and configuration details, see [Delegation & Voting Power](../guides/delegation-and-governance.md) and [Voting on Proposals](../guides/voting-on-proposals.md).

---

## Why Proposal Thresholds Exist

Any governance system faces a spam problem: if creating proposals is free, adversaries can flood the system with junk proposals that waste voter attention. Voter attention is the scarcest resource in a DAO — every spam proposal dilutes the community's capacity to evaluate real proposals.

Proposal thresholds solve this by requiring proposers to have meaningful **effective voting power** (ERC20Votes, which requires delegation). Tokens stay in your wallet — no escrow, no lockup, no claim process. You just need enough delegated voting power at the snapshot to prove you have skin in the game.

This replaced an earlier escrow-based model where proposers deposited tokens that were returned after execution. We moved away from escrow because it added unnecessary UX friction (approve + deposit + claim) for a problem that voting power checks solve more elegantly.

---

## Voting Power, Not Raw Balance

A subtle but important design choice: thresholds check **effective votes** (ERC20Votes), not raw token balance. This means:

- **Delegation matters**: You must delegate (often to yourself) to have any voting power. Holding tokens without delegation gives you zero votes and zero proposal rights.
- **Multipliers matter** (when enabled): DAO-scoped NFT multipliers affect your effective votes, so they also affect your ability to propose.
- **Tokens delegated away don't count**: If you delegate your voting power to someone else, you can't propose — they can.

We chose votes-based thresholds because they align proposing with the same "effective power" used for voting. This avoids the confusing case where someone holds enough tokens but can't propose because they haven't delegated, or where someone has delegated tokens from others and can propose with borrowed influence (which is a feature, not a bug — delegated influence is legitimate influence).

---

## The Cancellation Mechanism

If your effective votes drop below the threshold while your proposal is active, **anyone can cancel it**. This is a deliberate anti-manipulation mechanism.

The cancellation window covers Pending and Active states. Once a proposal reaches Succeeded (voting ended, passed), your votes no longer affect it. We chose this "safe point" design because:

- It prevents post-vote manipulation where someone bribes the proposer to dump tokens
- It provides certainty once the community has voted
- Anti-spam protection is only needed during the proposal lifecycle, not during execution

A key detail: the threshold is **snapshotted** when you create the proposal. If the DAO later raises the threshold, your proposal still uses the original value. This prevents mid-flight threshold changes from invalidating existing proposals.

---

## Cooldown: Rate Limiting Beyond Thresholds

Even with threshold requirements, a well-funded attacker could meet the threshold and spam proposals. The cooldown adds rate limiting: a configurable delay (default 5 minutes) between proposals per address.

Threshold and cooldown work together: threshold filters who can propose (economic filter), cooldown limits how often they can propose (temporal filter). Both must pass to create a proposal.

We considered making cooldown the only filter (no threshold), but rejected this because cooldown alone doesn't distinguish between valuable and spam proposals. Threshold adds an economic signal that cooldown lacks.

---

## Governance Profile Interactions

Not all governance modes enforce thresholds:

**COUNCIL_ONLY** proposals: Council members bypass the threshold. They're already trusted — adding a financial filter would be redundant friction.

**OPERATOR** governance: No threshold needed because the controller is already authorized.

**TOKEN + COMMUNITY_THRESHOLD**: Full threshold enforcement. This is where thresholds matter most — open communities where anyone might try to propose.

This tiered enforcement reflects the principle that governance controls should match the trust model. High-trust environments (councils, operators) need less friction; low-trust environments (open token voting) need more.

---

## The Minimum Voting Power to Vote

Separate from proposal thresholds, Sage enforces a minimum effective voting power to **cast a vote** (`minVotesToVote`, default: 1 SXXX effective vote).

This exists to prevent vote spam: without it, someone could split tokens across thousands of wallets and cast thousands of 0-weight votes, which clutters governance UIs and can exploit per-voter incentive schemes. The 1 SXXX minimum makes this attack economically impractical.

---

## Relationship to Other Parameters

Proposal thresholds interact with several other governance parameters:

**Quorum**: Threshold filters *who can propose*. Quorum ensures *enough people vote*. They solve different problems — threshold prevents proposal spam, quorum prevents minority capture.

**Voting delay**: The gap between proposal creation and voting start. Together with thresholds, this gives the community time to review proposals from qualified proposers.

**Voting period**: How long voting is open. Longer periods are more accessible but slower. Threshold quality filtering helps justify longer voting periods (fewer proposals, each worth reviewing).

---

## Tuning for Your Community

The default threshold (50 SXXX) is intentionally moderate — high enough to filter casual spam, low enough that engaged community members can participate. But different communities need different calibration:

**Higher thresholds** (100-1,000 SXXX) produce fewer proposals with higher average quality. They filter low-effort submissions but may exclude smaller holders. Good for mature DAOs with established contributors.

**Lower thresholds** (10-50 SXXX) produce more proposals with broader participation. They're more accessible but may need longer cooldowns to manage volume. Good for early-stage DAOs that want maximum engagement.

The threshold is governance-controlled, so communities can adjust as they learn what works.

---

## How This Connects

- [Governance Models](governance-models.md) — The broader governance system
- [Delegation & Voting Power](../guides/delegation-and-governance.md) — Getting voting power to meet thresholds
- [Voting on Proposals](../guides/voting-on-proposals.md) — How to participate once proposals exist
- [Voting Multipliers](voting-multipliers.md) — How multipliers affect effective votes and proposal eligibility
