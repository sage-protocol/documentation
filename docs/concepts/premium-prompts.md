# Premium Prompts

This document explains Sage Protocol's premium content system — why it exists, how encryption and licensing work, and why premium content is restricted to Personal DAOs by design.

For step-by-step instructions on publishing and buying premium content, see [Creating and Selling Premium Prompts](../guides/creating-and-selling-premium-prompts.md). For technical encryption details, see the [Encryption Architecture Reference](../reference/encryption-architecture.md).

---

## Why Premium Content Exists

Not all prompts should be free. Expert knowledge takes years to develop. Specialized prompts serve niche audiences willing to pay for quality. Creators deserve compensation for valuable work. And premium content revenue funds continued development.

Without a monetization path, the best prompt creators have no economic incentive to participate in the ecosystem. Bounties and tips help, but they require active patrons. Premium content lets creators earn passively from their expertise — anyone can purchase at any time without a specific bounty or relationship.

---

## How Premium Works

The core mechanism is straightforward: content is encrypted, and buyers purchase a license to decrypt it.

**Publishing**: The creator encrypts their prompt locally using AES-256-GCM, uploads the encrypted content to IPFS, and registers a price in the PersonalMarketplace contract. The encryption key is gated through Lit Protocol — it can only be retrieved by wallets that own the corresponding license receipt.

**Purchasing**: The buyer pays the listed price (in SXXX tokens) through the PersonalMarketplace contract. The contract transfers funds (net of protocol fee) to the creator and mints an ERC-1155 license receipt to the buyer.

**Accessing**: The buyer's wallet proves it owns the license receipt. Lit Protocol nodes verify this on-chain and release the decryption key. The content is decrypted locally — no central server ever holds the plaintext.

This design means the creator doesn't need to be online for buyers to access purchased content. And the on-chain license receipt is transferable — buyers can sell or gift their license.

---

## Why Personal DAOs Only

This is the most important design decision in the premium system: **premium content is only available for Personal DAOs**. Community DAOs are for public prompts only.

The reason is what we call the **governance paradox**: in community DAOs, members vote on adding prompts to the library. But with encrypted content, they cannot evaluate what they're voting to include. This breaks the fundamental assumption of governance — informed decision-making.

If a creator submits encrypted content to a community DAO, voters face an impossible choice: approve content they can't inspect, or reject it without knowing its quality. Neither outcome serves the community.

Personal DAOs solve this because the creator *is* the governance. They already know the content. There's no blind evaluation problem.

We considered several workarounds (preview excerpts, trusted reviewer access, time-limited decryption for voters) and rejected them all because they either leaked content or created complexity that undermined the system's simplicity. The clean solution is to separate the concerns: governance for public content, personal ownership for premium content.

For the full architectural decision record, see [ADR-001: Personal-Only Premium](/docs/adr/001-personal-only-premium.md).

---

## Revenue Models

Community DAOs can still monetize — just not through encrypted premium content:

- **Public prompts** build reputation and audience for the DAO
- **Bounties** funded by the DAO treasury produce public improvements
- **Tips** from grateful users flow to creators and the DAO treasury
- **Protocol fee revenue** from premium purchases in Personal DAOs contributes to the broader ecosystem

For individual creators, the recommended strategy is a hybrid approach: free content builds audience and reputation; premium content monetizes deep expertise. The free tier attracts users who discover the premium tier through demonstrated quality.

---

## The Encryption Architecture

Premium prompts use hybrid encryption combining local AES-256-GCM with Lit Protocol access control. This is a deliberate layering:

**AES-256-GCM** (symmetric, local) handles the actual content encryption. It's fast and well-understood.

**Lit Protocol** (decentralized key management) handles access control. Instead of storing decryption keys on a server, Lit nodes verify on-chain conditions (does this wallet own the ERC-1155 receipt?) and release the key only if conditions are met.

The access condition is intentionally simple: `PersonalLicenseReceipt.balanceOf(buyer, receiptId) > 0`. This keeps the smart contract interaction minimal and auditable.

A key implementation detail: the `receiptId` is a deterministic `uint256` derived from `(creator, key)` via `keccak256("SAGE-PERSONAL-LICENSE", creator, keyBytes32)`, and Lit requires this as a **decimal string**, not hex. This is a common integration gotcha that causes "uint parse error" failures.

---

## License Receipt Properties

ERC-1155 license receipts have several properties worth understanding:

**Transferable**: Buyers can sell or gift their license. This creates a secondary market for access, which is unusual in content marketplaces. We chose transferability because it gives buyers genuine ownership — you bought it, you can do what you want with it.

**Semi-fungible**: All licenses for the same `(creator, key)` are identical. If a creator sells 100 licenses, all 100 holders have the same access. This is simpler than per-buyer unique licenses and matches how most content purchases work.

**On-chain provable**: Any contract can check `balanceOf(buyer, receiptId) > 0`. This makes license ownership composable — other systems can gate features or access based on Sage license ownership.

---

## Tradeoffs

**Personal-only restriction**: This limits the premium revenue model to individual creators. DAOs can't sell encrypted content through governance. We believe this is the right tradeoff (governance requires inspectability), but it does mean community DAOs must find other revenue paths.

**Lit Protocol dependency**: Decryption depends on Lit Protocol nodes being available and honest. If Lit goes down, purchased content becomes temporarily inaccessible (though the encrypted content on IPFS and the license receipt on-chain remain permanent). This is a practical availability risk, not a trust risk — the cryptographic guarantees are sound.

**Price in SXXX only**: Premium pricing is denominated in SXXX tokens, which creates price volatility. We chose SXXX over stablecoins to create token demand, but this means creators and buyers must manage token price risk.

---

## How This Connects

- [Creating and Selling Premium Prompts](../guides/creating-and-selling-premium-prompts.md) — Step-by-step guide
- [Encryption Architecture Reference](../reference/encryption-architecture.md) — Technical encryption details
- [ADR-001: Personal-Only Premium](/docs/adr/001-personal-only-premium.md) — Architectural decision record
- [Economic Thesis](economic-thesis.md) — How premium fits the broader economic model
- [Bounties](bounties.md) — Alternative revenue through contributions
