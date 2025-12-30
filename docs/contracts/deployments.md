# Deployment Addresses

This page lists all deployed smart contract addresses for the Sage Protocol across different networks.

---

## Base Sepolia Testnet

The following contracts are deployed on Base Sepolia testnet for testing and development:

| Component | Address | Type | Purpose |
|-----------|---------|------|---------|
| **SXXX Token** | `0xA09e10Ce709A2a0895Cebb6595089BFCEBb88e63` | ERC20Votes | Governance token for delegation and voting |
| **SubDAO Factory** | `0x9a3a5bA9A71d40815d49b0a98AE7A019994D6d89` | Diamond | Creates new SubDAOs using minimal proxies |
| **Library Registry** | `0x3490D8942f21e47D4Bc327c4eFcD6dEbE3B24EAB` | Registry | Global directory for library manifests |
| **Prompt Registry** | `0xda97A508CADfdE1d205F7Ee40D5871E7A047EEfc` | Implementation | Stores prompts with versioning and attribution |
| **Governor** | `0x545BBBbD4ec74063473d0Bb91F38514e67AeF41a` | Implementation | Governance contract template |
| **Timelock** | `0x4bAAe2b995F3c7b593896968eDBCf5a75e0bDbBA` | Implementation | Time-delayed execution for proposals |
| **Boost Manager (Merkle)** | `0x5fEA0566570A57B6e30e713D8E6aF4963b1865bA` | Incentives | Merkle-based reward distribution |
| **Boost Manager (Direct)** | `0xAE7F450Bd7fC9cF1459f160c550B16E4842A3C98` | Incentives | Direct reward distribution |
| **Premium Prompts** | `0x9cFfd7E277b167c55760316C67f50FDc8005c590` | Marketplace | Paid prompt access and revenue splits |
| **Safe Master Copy** | `0x87518d407235196c82B6C8F491d94f554f705435` | Treasury | Gnosis Safe template for treasuries |
| **Template Module** | `0x47e7F7B9c5AB13F2B76801C983cE1825975D321f` | Configuration | DAO configuration templates |
| **VotingMultiplierNFT** | `0xdd4bacBB3d84c0839d19897E8545B8d88932C73b` | NFT | Voting power multiplier NFTs |
| **SageAuctionHouse** | `0x5BEe8C80a3c4e77CaE424bf2976506dFc49Cc739` | Auction | Nouns-style NFT auctions |
| **SimpleContributorSystem** | `0xBDe855A6ce1df109B55088A37a986B3bD08AA47b` | Reputation | Contributor staking and reputation tracking |
| **SimpleBountySystem** | `0x75C992dc53790b44B710d16BD096a40fAA651Ff7` | Bounties | Task bounties with escrow and submissions |
| **SoulboundBadge** | `0xE1c57F11Ce47bc70B59d66172Cdc9263B17DeA38` | NFT | Non-transferable achievement badges |
| **ReputationLedger** | `0xc6BaD2C006C479a3dB53065eD943Dd79Eb846944` | Reputation | On-chain author stats with merkle settlements |
| **SXXX Faucet** | `0xD7DF6CF9bab0e0f2dD858B579909E752227A3455` | Utility | Testnet token faucet |
| **MockUSDC** | `0x37021A43CE0ed83BFDB0194e05e7dFF7bEcAcB05` | ERC20 | Test USDC for development |

### Network Information

- **Network Name**: Base Sepolia
- **Chain ID**: 84532
- **RPC URL**: https://sepolia.base.org
- **Block Explorer**: https://sepolia.basescan.org
- **Subgraph Endpoint**: (To be deployed)

---

## Base Mainnet (Coming Soon)

Mainnet deployment addresses will be published here after the public LBP launch.

**Expected Launch**: Q1 2025

**Pre-Launch Checklist**:
- ✅ Security audits completed (Cyfrin, OpenZeppelin)
- ✅ Testnet deployment live and tested
- ⏳ LBP parameters finalized
- ⏳ Subgraph deployed and indexed
- ⏳ MCP server production-ready
- ⏳ Treasury multisig configured

---

## Verifying Contract Addresses

You can verify these addresses and view contract source code on [Basescan](https://basescan.org):

```bash
# Using the Sage CLI
sage contract verify --network base-sepolia --address <CONTRACT_ADDRESS>

# Or manually on Basescan
# 1. Navigate to https://sepolia.basescan.org/address/<ADDRESS>
# 2. Click "Contract" tab
# 3. View verified source code and ABI
```

---

## Subgraph Endpoints

The Sage subgraph indexes all on-chain events for fast querying:

### Base Sepolia

```
GraphQL Endpoint: (To be deployed)
Queries/day: Unlimited (development)
```

Example query:

```graphql
query LatestLibraries {
  libraries(
    first: 10
    orderBy: updatedAt
    orderDirection: desc
  ) {
    id
    name
    manifestCid
    subdao {
      name
      governor
    }
    updatedAt
  }
}
```

### Base Mainnet

```
GraphQL Endpoint: (Coming after mainnet launch)
```

---

## IPFS Infrastructure

### Public Gateways

Primary gateway for content retrieval:

- **Gateway URL**: https://gateway.sageprotocol.io
- **Managed Worker**: https://ipfs-worker.sageprotocol.io
- **Fallback Gateway**: https://w3s.link/ipfs/

### Pinning Services

Sage uses a managed Cloudflare Worker for pinning with credit-based payment:

- **Worker Endpoint**: https://ipfs-worker.sageprotocol.io
- **Credit Purchase**: Via CLI (`sage ipfs buy-credits`)
- **Pricing**: ~$0.01-0.10 per GB/month

---

## CLI Configuration

To configure the Sage CLI to use these addresses:

```bash
# Connect to Base Sepolia testnet
sage config set --network base-sepolia

# Verify connection
sage config show

# Output:
# Network: base-sepolia
# RPC: https://sepolia.base.org
# Factory: 0x9a3a5bA9A71d40815d49b0a98AE7A019994D6d89
# Library Registry: 0x3490D8942f21e47D4Bc327c4eFcD6dEbE3B24EAB
# SXXX Token: 0xA09e10Ce709A2a0895Cebb6595089BFCEBb88e63
```

---

## Treasury Addresses

### Main Protocol Treasury

- **Safe Address**: (To be deployed on mainnet)
- **Signers**: 5-of-9 multisig (core team, advisors, community)
- **Holdings**: SAGE, USDC, ETH
- **LaunchGate**: (To be deployed)
- **TreasuryWrapper**: (To be deployed)

### Example DAO Treasuries

DAOs create their own treasuries during deployment. View all DAOs:

```bash
sage dao list --network base-sepolia
```

---

## Contract ABIs

All contract ABIs are available in the monorepo:

```bash
# Clone the repo
git clone https://github.com/velinusplatform/sage-protocol
cd sage-protocol

# ABIs are in packages/contracts/artifacts
ls packages/contracts/artifacts/contracts/
```

Or fetch via CLI:

```bash
sage contract abi --name SubDAOFactory --output factory-abi.json
```

---

## Security & Audits

All contracts have undergone professional security audits:

- **Cyfrin**: [View Report](https://github.com/sageprotocol/audits/cyfrin-2024.pdf)
- **OpenZeppelin**: [View Report](https://github.com/sageprotocol/audits/oz-2024.pdf)

**Bug Bounty Program**: Up to $50,000 USDC for critical vulnerabilities. See [bug bounty details](https://github.com/sageprotocol/security).

---

## Latest Addresses

Deployment addresses are automatically updated after each deployment. For the absolute latest addresses, check:

```bash
# Via CLI
sage contract list --network base-sepolia --latest

# Or view in docs
cat docs/appendix/addresses.latest.md
```

**Note**: Always verify contract addresses through multiple sources before interacting with them. Never trust addresses from unofficial sources.

---

## Contract Upgrades

Sage uses a combination of upgradeable and immutable contracts:

### Immutable Contracts

- SXXX Token (ERC20Votes)
- Library Registry
- Timelock implementations

### Upgradeable Contracts (via Governance)

- SubDAO Factory (Diamond proxy pattern)
- Governor implementations (via Timelock)
- Treasury system (LaunchGate, TreasuryWrapper)

All upgrades require:
1. Governance proposal
2. Community vote
3. Timelock delay (≥24 hours on mainnet)
4. Multi-signature approval (for critical contracts)

---

## Developer Resources

### Quick Links

- **GitHub**: https://github.com/velinusplatform/sage-protocol
- **Documentation**: https://docs.sageprotocol.io
- **Discord**: (Coming soon)
- **Twitter**: https://x.com/VelinusSage

### Example Integration

```typescript
import { SageSDK } from '@sage-protocol/sdk'

// Initialize SDK
const sdk = new SageSDK({
  network: 'base-sepolia',
  rpcUrl: 'https://sepolia.base.org',
  contracts: {
    factory: '0x9a3a5bA9A71d40815d49b0a98AE7A019994D6d89',
    libraryRegistry: '0x3490D8942f21e47D4Bc327c4eFcD6dEbE3B24EAB',
  }
})

// Fetch latest library
const library = await sdk.getLibrary('my-prompts')
console.log(library.manifestCid)
```

---

For questions about deployments or integration support, follow [@VelinusSage](https://x.com/VelinusSage) on X or open an issue on [GitHub](https://github.com/velinusplatform/sage-protocol/issues).
