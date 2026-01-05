# Premium Prompts

Premium prompts let creators monetize their expertise. The content is encrypted - buyers purchase a license to decrypt and use it.

## Why Premium?

Not all prompts should be free:

- Expert knowledge took years to develop
- Specialized prompts serve niche audiences
- Creators deserve compensation for valuable work
- Premium content funds continued development

Sage supports premium content through **Personal DAOs** where the creator controls both the content and governance.

## Personal Premium

Individual creators sell prompts directly to buyers.

```
Creator ──publish──▶ Marketplace ──encrypt──▶ IPFS
                          │
                          ▼
Buyer ──purchase──▶ License Receipt (ERC1155)
                          │
                          ▼
Buyer ──decrypt──▶ Full Prompt Content
```

### Publishing Premium Content

```bash
# Publish a premium prompt
sage personal premium publish trading-strategy \
  --file ./prompts/trading-strategy.md \
  --price 25
```

The CLI:

1. Encrypts the prompt content using Lit Protocol
2. Uploads encrypted content to IPFS
3. Registers the prompt in PersonalMarketplace contract
4. Sets the price (in SXXX tokens)

### Buying Premium Content

```bash
# Check the price
sage personal premium price 0xCreator trading-strategy
# Output: 25 SXXX (+ 2.5 SXXX protocol fee)

# Purchase license
sage personal premium buy 0xCreator trading-strategy -y
```

Buyers receive an ERC1155 license receipt token proving ownership.

### Accessing Purchased Content

```bash
# Decrypt and view
sage personal premium access 0xCreator trading-strategy

# Output: Full prompt content appears
```

The CLI:

1. Checks you own the license receipt
2. Uses Lit Protocol to decrypt
3. Displays the prompt content

### Pricing

Prices are set in SXXX tokens:

| Price        | Typical Use                  |
| ------------ | ---------------------------- |
| 5-25 SXXX    | Simple prompts, templates    |
| 25-100 SXXX  | Domain expertise, workflows  |
| 100-500 SXXX | Premium strategies, systems  |
| 500+ SXXX    | Enterprise, consulting-grade |

**Protocol fee:** 10% goes to the protocol treasury.

### Managing Listings

```bash
# Update price
sage personal premium price --id 42 --new-price 30

# Remove from sale (existing licenses still work)
sage personal unlist trading-strategy -y

# View your listings
sage personal list
```

## Why Personal DAOs Only

Premium content is **only available for Personal DAOs** by design. Community DAOs are for public prompts only.

### The Governance Paradox

In community DAOs, members vote on adding prompts to the library. With encrypted content, they cannot evaluate what they're voting to include - this breaks the governance model's core assumption of informed decision-making.

```
Creator ──submit encrypted──▶ DAO Governance ──vote──▶ ???
                                    │
                                    ▼
                        Members can't decrypt to evaluate
                        (they don't own licenses yet)
```

### Why Personal DAOs Work

Personal DAOs solve this because:

- The creator IS the governance (single voter)
- The creator already knows the content
- No blind evaluation problem exists

### What About DAO Revenue Sharing?

DAOs can still monetize through:

- **Public prompts** - Community-curated free content builds reputation
- **Bounties** - DAOs fund prompt creation, results are public
- **Staking rewards** - Token holders earn from protocol fees

For premium monetization, use a Personal DAO where you control both content and governance.

See [ADR-001: Personal-Only Premium](/docs/adr/001-personal-only-premium.md) for the full architectural decision.

## Encryption (Lit Protocol)

Premium prompts use **hybrid encryption** combining AES-256-GCM and Lit Protocol:

1. **Content encryption:** Prompt encrypted locally with AES-256-GCM symmetric key
2. **Key gating:** Symmetric key encrypted with Lit Protocol access conditions
3. **Access condition:** Must own the ERC1155 license receipt
4. **Decryption:** Lit nodes verify ownership, release key; content decrypted locally

This means:

- Content is encrypted at rest (on IPFS)
- No central server holds decryption keys
- License ownership is on-chain and verifiable
- Creator doesn't need to be online for decryption

For technical details on the encryption architecture, data structures, and implementation, see the [Encryption Architecture Reference](../reference/encryption-architecture.md).

## License Receipts (ERC1155)

License receipts are semi-fungible tokens:

```solidity
PersonalLicenseReceipt.balanceOf(buyer, promptId) > 0
```

Properties:

- **Transferable:** Can sell or gift your license
- **Provable:** On-chain proof of purchase
- **Composable:** Other contracts can check ownership

## When to Use Premium

**Use premium for:**

- Specialized domain expertise
- Prompts with significant R&D investment
- Content serving professional/enterprise users
- Monetizing your prompt engineering skills

**Keep free for:**

- Basic/foundational prompts
- Community-building content
- Prompts you want maximally distributed
- Content meant to attract users to premium

## Free vs Premium Strategy

Many creators use a hybrid approach:

| Tier    | Content                  | Price        |
| ------- | ------------------------ | ------------ |
| Free    | Basic prompts, templates | 0            |
| Free    | Community contributions  | 0            |
| Premium | Expert workflows         | 25-100 SXXX  |
| Premium | Domain-specific systems  | 100-500 SXXX |

Free content builds audience; premium content monetizes expertise.

## CLI Reference

```bash
# Publish premium
sage personal premium publish <key> --file <path> --price <amount>

# Check price (as buyer)
sage personal premium price <creator> <key>

# Purchase license
sage personal premium buy <creator> <key> -y

# Decrypt content (after purchase)
sage personal premium access <creator> <key>

# Update price (as creator)
sage personal premium price --id <id> --new-price <amount>

# Unlist (as creator)
sage personal premium unlist <key> -y

# View your licenses
sage personal my-licenses

# List premium prompts
sage personal list --creator 0x...
```

## Related

- [Creating and Selling Premium Prompts](../guides/creating-and-selling-premium-prompts.md) - Full guide
- [Personal/Vault Library](../guides/creating-your-first-prompt-library.md) - Free publishing
- [Bounties](./bounties.md) - Earning through contributions
