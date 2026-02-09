# CLI Command Reference

Complete reference for all Sage CLI commands.

---

## Command Aliases

| Full Command | Aliases |
|---|---|
| `sage library` | `lib`, `l` |
| `sage governance` | `gov`, `g` |
| `sage subdao` | `sd`, `sub` |
| `sage treasury` | `treas`, `t` |
| `sage timelock` | `tl` |
| `sage sxxx` | `sx`, `token` |
| `sage wallet` | `w` |
| `sage config` | `cfg` |
| `sage context` | `ctx` |

---

## Library Commands

### `sage library quickstart`

Create a new library with prompts: scan directory → upload to IPFS → create DAO → register manifest.

| Option | Type | Description |
|---|---|---|
| `--name <string>` | string | Library name |
| `--from-dir <path>` | string | Directory to scan |
| `--governance <mode>` | string | `team` or `community` |
| `--type <method>` | string | Wallet connection method |
| `--path <derivation>` | string | HD derivation path |
| `--index <number>` | number | Account index |

```bash
sage library quickstart --name "My Library" --from-dir ./prompts
```

---

## Diagnostics

### `sage doctor`

Verify CLI configuration: wallet, RPC, IPFS worker, contracts, subgraph sync.

```bash
sage doctor
```

---

## Project Commands

### `sage project template`

| Option | Default | Description |
|---|---|---|
| `--type <type>` | `basic` | `basic`, `mcp`, `agent` |
| `--out <path>` | `manifest.json` | Output path |

### `sage project add-prompt`

| Option | Description |
|---|---|
| `--manifest <path>` | Manifest file |
| `--file <path>` | Prompt markdown file |
| `--key <string>` | Unique prompt key |
| `--name <string>` | Human-readable name |
| `--upload` | Upload to IPFS and add CID |

### `sage project preview <path>`

Preview manifest before publishing.

### `sage project status --dao <address>`

Check library status in a DAO.

### `sage project push <path>`

Upload library and create governance proposal.

| Option | Description |
|---|---|
| `--dao <address>` | DAO address |
| `--pin` | Pin on IPFS |
| `--wait` | Wait for upload |
| `--warm` | Warm caches |
| `--exec` | Execute directly (team mode) |

---

## Skills Management

| Command | Description |
|---|---|
| `sage prompts init` | Initialize workspace |
| `sage prompts list` | List workspace skills |
| `sage prompts variant <key> [suffix]` | Create variant |
| `sage prompts agents-sync` | Sync to AGENTS.md |
| `sage prompts import <source>` | Import skills |
| `sage prompts export <key> --as <format>` | Export skill |
| `sage prompts publish [key] --dao <address>` | Publish to on-chain library |
| `sage prompts doctor [key] --dao <address>` | Validate skills |

---

## DAO Commands

### `sage dao create-playbook`

| Playbook | Description |
|---|---|
| `creator` | Solo operator |
| `squad` | Safe multisig (3–10 members) |
| `community` | Token voting (standard) |
| `community-long` | Token voting (long delays, high quorum) |

| Option | Description |
|---|---|
| `--playbook <type>` | Required |
| `--name <string>` | DAO name |
| `--dry-run` | Generate plan only |
| `--apply <path>` | Apply plan file |

### `sage dao create`

Custom DAO deployment.

| Option | Description |
|---|---|
| `--name`, `--symbol`, `--token-type`, `--initial-supply` | Token config |
| `--voting-delay`, `--voting-period`, `--proposal-threshold`, `--quorum` | Governance params |
| `--profile-cid` | IPFS CID for profile metadata |

### `sage dao list`

List DAOs.

### `sage dao info <address>`

DAO details.

### `sage dao profile get <address>`

Profile metadata. `--json` for JSON.

### `sage dao profile set <address> --cid <cid>`

Update profile CID (governance proposal).

### `sage dao profile upload <address>`

Upload profile to IPFS.

| Option | Description |
|---|---|
| `--file <path>` | Profile JSON |
| `--name`, `--description`, `--avatar`, `--website`, `--twitter`, `--discord` | Fields |
| `--apply` | Also submit proposal |

---

## Proposals & Governance

| Command | Description |
|---|---|
| `sage proposals inbox --dao <address>` | List proposals |
| `sage proposals preview <id> --dao <address>` | Proposal details |
| `sage proposals vote <id> for\|against\|abstain --dao <address>` | Vote |
| `sage proposals execute <id> --dao <address>` | Execute after timelock |
| `sage proposals status --dao <address>` | Status + next actions |
| `sage governance preflight --subdao <address>` | Delegation/gate check |
| `sage governance diag --subdao <address>` | Full diagnostics |

---

## Profile Commands

### `sage profile set`

| Option | Description |
|---|---|
| `--name <name>` | Display name (max 50) |
| `--bio <bio>` | Bio (max 160) |
| `--avatar <url>` | Avatar URL or CID |
| `--twitter`, `--github`, `--farcaster`, `--website` | Social links |
| `--clear` | Clear all fields |

### `sage profile interactive`

Interactive setup wizard. Alias: `sage profile wizard`.

---

## Personal Premium

| Command | Description |
|---|---|
| `sage personal sell --file <path> --price <usdc>` | Publish premium prompt |
| `sage personal buy --id <id>` | Purchase access |
| `sage personal access --id <id>` | Decrypt and view |
| `sage personal my-licenses` | List owned licenses |
| `sage personal list` | List available prompts |
| `sage personal price --id <id> --new-price <amount>` | Update price |
| `sage personal unlist --id <id>` | Unlist prompt |

---

## Reputation

```bash
sage reputation status <address>
sage reputation leaderboard --limit 20
sage reputation achievements <address>
sage reputation check <address> --min-badges 1
```

---

## IPFS

| Command | Description |
|---|---|
| `sage ipfs upload <file>` | Upload |
| `sage ipfs download <cid>` | Download (`--output <path>`) |
| `sage ipfs pin <cid>` | Pin content |
| `sage ipfs credits` | Credit balance |
| `sage ipfs fetch <cid>` | Fetch (`--output`, `--timeout`) |

---

## NFT (Admin)

| Command | Description |
|---|---|
| `sage nft doctor` | Check config |
| `sage nft list-tiers --dao <address>` | List tiers |
| `sage nft my-multiplier --dao <address>` | Voting multiplier |
| `sage nft tier create` | Create tier (ADMIN) |
| `sage nft mint --tier <id> --to <address>` | Mint (MINTER) |
| `sage nft public-mint --tier <id>` | Public mint |
| `sage nft grant-role --to <address> --role MINTER` | Grant role |

---

## Multiplier (End-User)

| Command | Description |
|---|---|
| `sage multiplier status --dao <address>` | Config |
| `sage multiplier calculate <address> --dao <address>` | Power breakdown |
| `sage multiplier describe --dao <address>` | Tier details |
| `sage multiplier propose-tier` | Governance payload |
| `sage multiplier auction status\|bid\|settle` | Auction ops |

---

## Boost, Bounty, Auction, SBT, Council, Safe, Timelock, Token, MCP

| Command Group | Key Commands |
|---|---|
| `sage boost` | `create`, `status`, `claim`, `set-root`, `fund`, `finalize` |
| `sage bounty` | `create`, `list`, `claim`, `submit`, `approve`, `wizard`, `set-mode`, `fund` |
| `sage auction` | `status`, `bid`, `settle` |
| `sage sbt` | `doctor`, `mint`, `revoke`, `propose-mint`, `list-reasons` |
| `sage council` | `doctor`, `set-config`, `allow`, `show`, `exec` |
| `sage safe` | `propose`, `status`, `doctor`, `propose-bounty` |
| `sage timelock` | `doctor`, `schedule`, `execute`, `list`, `cancel`, `fix-roles`, `execute-batch` |
| `sage sxxx` | `balance`, `delegate`, `approve`, `transfer` |
| `sage mcp` | `start`, `stdio` |

---

## Environment Variables

| Variable | Description |
|---|---|
| `RPC_URL` | RPC endpoint |
| `PRIVATE_KEY` | Wallet private key |
| `WALLET_TYPE` | `privy`, `cast`, `local` |
| `IPFS_WORKER_URL` | IPFS worker endpoint |
| `SUBDAO` | Default SubDAO address |
| `SAGE_YES` | Auto-confirm (`1`) |
| `SAGE_FORCE` | Skip confirmations (`1`) |

---

## Exit Codes

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | General error |
| `2` | Configuration error |
| `3` | Network error |
| `4` | Validation error |
| `5` | Permission denied |
