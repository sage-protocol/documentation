# Contracts Architecture

Smart contract architecture for Sage Protocol.

---

## Modules

### SubDAOFactory (`SubDAOFactoryOptimized.sol`)

Entry point for DAO creation using gas-efficient minimal proxies (EIP-2535 Diamond pattern).

Deploys:
- Governor + Timelock pair
- Library and prompt registries
- Initial roles for executors and treasuries

### SubDAO (`SubDAO.sol`)

DAO contract representing a governance domain.

References:
- Governor + Timelock pair
- LibraryRegistry and PromptRegistry
- Treasury contracts or wrappers

### LibraryRegistry (`LibraryRegistry.sol`)

Tracks library manifests (CIDs) and registry-level metadata. Stores `DAO → libraryIdHash → LibraryInfo(manifestCID, version, nonce)`. Only the DAO's Timelock can update manifests.

### PromptRegistry (`PromptRegistry.sol`)

Tracks prompts and their relationships (forks, updates, attribution) at per-prompt granularity.

### Treasury Contracts

Hold assets with ARBAC-style roles and selector allowlists. Coordinate with the DAO's Timelock for execution.

---

## Voting Power & Multipliers

| Layer | Mechanism | Description |
|---|---|---|
| Base | ERC20Votes (SXXX) | Defines voting power, quorum, proposal thresholds |
| Multiplier | VotingMultiplierNFT | Per-DAO boost via transferable NFTs |
| Wrapper | MultipliedVotes | Combines base token votes with DAO-specific multiplier |

Delegation operates on the base token. The multiplier layer affects how much voting power a delegated balance represents.

---

## Governance Flow

```
Proposal Creation → Voting → Queue (Timelock) → Execution
```

1. **Propose**: User or agent prepares calldata (library manifest update, treasury action, config change).
2. **Vote**: Token holders vote through the Governor. Parameters: delay, period, quorum, threshold.
3. **Queue**: Passed proposals enter the Timelock.
4. **Execute**: After delay, the Timelock executes registry updates or treasury calls.

---

## Governance Profiles

| Kind | Proposal Access | Execution Access |
|---|---|---|
| `OPERATOR` | `COUNCIL_ONLY` | `COUNCIL_ONLY` |
| `TOKEN` | `COMMUNITY_THRESHOLD` | `ANYONE` |

---

## Storage & Discovery

| Component | Description |
|---|---|
| IPFS + CIDs | Manifests and prompts stored on IPFS; registries store CIDs |
| Subgraph | Indexes DAO, registry, and marketplace events; GraphQL API |
| MCP Server | Model-native interface for discovery, fetching, validation |

---

## Marketplace Contracts

### PersonalMarketplace (`PersonalMarketplace.sol`)

Manages SXXX-priced licenses for personal premium prompts. Maps `(creator, key)` to prices. Mints ERC1155 receipts via `PersonalLicenseReceipt`.

### PersonalLicenseReceipt (`PersonalLicenseReceipt.sol`)

ERC1155 token representing premium license ownership. Used in Lit access conditions.

### PremiumPrompts (Deprecated)

Not supported for DAO-level premium due to governance paradox (members cannot evaluate encrypted content before voting). See [ADR-001](/docs/adr/001-personal-only-premium.md). Remains deployed for legacy Lit balance checks.

---

## Diamond Architecture (SubDAOFactory)

```
Diamond.sol
├── FactoryCoreFacet.sol
├── SubDAOFactoryCreateFacet.sol
├── SubDAOFactoryForkFacet.sol
├── SubDAOFactoryGovernanceFacet.sol
├── SubDAOFactoryOperatorFacet.sol
├── SubDAOFactoryPromptFacet.sol
└── SubDAOFactoryStableFacet.sol

LibFactoryStorage.sol — Shared storage layout
```

---

## See Also

- [Deployment Addresses](deployments.md)
- [Contract Architecture (monorepo)](../../../contracts/docs/ARCHITECTURE.md)
- [Governance](../concepts/governance-models.md)
