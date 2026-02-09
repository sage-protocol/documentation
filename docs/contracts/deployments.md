# Deployment Addresses

Contract addresses for Sage Protocol networks.

---

## Base Sepolia Testnet

| Component | Address | Type |
|---|---|---|
| SXXX Token | `0xA09e10Ce709A2a0895Cebb6595089BFCEBb88e63` | ERC20Votes |
| SubDAO Factory | `0x9a3a5bA9A71d40815d49b0a98AE7A019994D6d89` | Diamond |
| Library Registry | `0x3490D8942f21e47D4Bc327c4eFcD6dEbE3B24EAB` | Registry |
| Prompt Registry | `0xda97A508CADfdE1d205F7Ee40D5871E7A047EEfc` | Implementation |
| Governor | `0x545BBBbD4ec74063473d0Bb91F38514e67AeF41a` | Implementation |
| Timelock | `0x4bAAe2b995F3c7b593896968eDBCf5a75e0bDbBA` | Implementation |
| Boost Manager (Merkle) | `0x5fEA0566570A57B6e30e713D8E6aF4963b1865bA` | Incentives |
| Boost Manager (Direct) | `0xAE7F450Bd7fC9cF1459f160c550B16E4842A3C98` | Incentives |
| Premium Prompts | `0x9cFfd7E277b167c55760316C67f50FDc8005c590` | Marketplace |
| Safe Master Copy | `0x87518d407235196c82B6C8F491d94f554f705435` | Treasury |
| Template Module | `0x47e7F7B9c5AB13F2B76801C983cE1825975D321f` | Configuration |
| VotingMultiplierNFT | `0xdd4bacBB3d84c0839d19897E8545B8d88932C73b` | NFT |
| SageAuctionHouse | `0x5BEe8C80a3c4e77CaE424bf2976506dFc49Cc739` | Auction |
| SimpleContributorSystem | `0xBDe855A6ce1df109B55088A37a986B3bD08AA47b` | Reputation |
| SimpleBountySystem | `0x75C992dc53790b44B710d16BD096a40fAA651Ff7` | Bounties |
| SoulboundBadge | `0xE1c57F11Ce47bc70B59d66172Cdc9263B17DeA38` | NFT |
| SXXX Faucet | `0xD7DF6CF9bab0e0f2dD858B579909E752227A3455` | Utility |
| MockUSDC | `0x37021A43CE0ed83BFDB0194e05e7dFF7bEcAcB05` | ERC20 |

### Network Information

| Property | Value |
|---|---|
| Network | Base Sepolia |
| Chain ID | `84532` |
| RPC URL | `https://sepolia.base.org` |
| Block Explorer | `https://sepolia.basescan.org` |

---

## Base Mainnet

Mainnet addresses are published after deployment. See `packages/contracts/deployment/addresses/mainnet.json`.

---

## Subgraph Endpoints

### Base Sepolia

```
Goldsky: https://api.goldsky.com/api/public/project_cmhxp0fppsbdd01q56xp2gqw9/subgraphs/sxxx-protocol/<version>/gn
```

### Base Mainnet

Published after mainnet deployment.

---

## Contract ABIs

```bash
# From monorepo artifacts
ls packages/contracts/artifacts/contracts/

# Via CLI
sage contract abi --name SubDAOFactory --output factory-abi.json
```

---

## Verification

Verify contracts on [Basescan](https://sepolia.basescan.org):

```bash
sage contract verify --network base-sepolia --address <ADDRESS>
```

---

## See Also

- Latest addresses: [addresses.latest.md](/docs/appendix/addresses.latest.md)
- [Architecture](architecture.md)
- [CLI Configuration](/docs/development/cli-reference.md) — `sage config addresses import`
