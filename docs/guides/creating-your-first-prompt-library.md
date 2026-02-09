# Create Your First Prompt Library

In this tutorial, we will set up a wallet, create a prompt workspace, author a prompt, publish it through a DAO, and verify that AI agents can access it via the MCP server. Along the way, we will use the Sage CLI's governance workflow and IPFS publishing pipeline.

## Prerequisites

- Sage CLI installed (`sage --version` shows `0.6.x` or later)
- Base Sepolia ETH for gas (see [Quick Start](../../../docs/getting-started/quick-start.md) if you need testnet funds)

## Step 1: Set up the wallet

Let's initialize and verify our wallet connection.

```bash
sage wallet init
```

Follow the prompts to connect via Privy. Once complete, verify:

```bash
sage wallet doctor
```

You should see your wallet address, chain ID `84532` (Base Sepolia), and green checkmarks for RPC connectivity and wallet access.

## Step 2: Get SXXX tokens

We need SXXX governance tokens for DAO creation and publishing.

```bash
sage sxxx faucet
```

You should see:

```
✓ Sent SXXX to 0xYourAddress
```

Confirm the balance:

```bash
sage sxxx balance
```

You should see at least 5000 SXXX.

## Step 3: Delegate voting power

We need to delegate our SXXX voting power to ourselves so it counts in governance.

```bash
sage sxxx delegate-self
```

You should see:

```
✓ Delegated voting power to 0xYourAddress
```

## Step 4: Initialize a prompts workspace

```bash
sage prompts init
```

You should see:

```
✓ Created .sage/workspace.json
✓ Created prompts/ directory
```

Notice that the CLI created a `prompts/` folder and a `.sage/workspace.json` configuration file in your current directory.

## Step 5: Author a prompt

Create a prompt file in the `prompts/` directory:

```bash
cat > prompts/hello-world.md << 'EOF'
---
title: Hello World
description: Simple test prompt for Sage
---

You are a helpful assistant. Say hello to the user and ask one clarifying question about their goal.
EOF
```

Let's verify the workspace detects our new file:

```bash
sage prompts status
```

You should see:

```
Changes:
  +1 added: hello-world.md

Ready to publish.
```

## Step 6: Publish through a DAO

Now we will publish our prompt. The CLI will create a DAO (if we don't have one), upload to IPFS, and register the manifest on-chain.

```bash
sage library quickstart --name "My Library" --from-dir ./prompts
```

You should see output similar to:

```
✓ Created DAO at 0xAbC123... (personal governance)
✓ Uploaded 1 prompt to IPFS
✓ Manifest CID: bafybeig...
✓ Registered on-chain
✓ Saved alias "My Library"
```

Notice that the CLI created a personal governance DAO, which executes updates immediately without a voting period. (See [Governance Modes](../core-concepts/governance-modes.md) for community and council options.)

## Step 7: Verify the published library

Let's confirm our library is live on-chain:

```bash
sage project view-prompts <manifestCID>
```

Replace `<manifestCID>` with the CID printed in Step 6.

You should see your `hello-world` prompt listed with its own IPFS CID.

## Step 8: Start the MCP server

Now let's make our library accessible to AI agents via the Model Context Protocol.

```bash
sage mcp start --port 3000
```

You should see:

```
✓ MCP server listening on port 3000
```

Agents connected to this MCP server can now discover your DAO, search your prompts by keyword, and fetch the latest governed manifest — all without knowing contract addresses or CIDs directly. (See [Using the MCP Server](using-the-mcp-server.md) for Claude Desktop integration and advanced configuration.)

## What we accomplished

We set up a wallet on Base Sepolia, authored a prompt, published it through a governed DAO, and started an MCP server so AI agents can access our library. The complete pipeline — from local markdown to governed, agent-ready content — is now in place. From here, you can:

- [Add more prompts and publish updates](publishing-and-versioning-prompts.md) — manage your growing library
- [Create a SubDAO with team governance](creating-a-subdao.md) — share control with collaborators
- [Explore delegation and voting](delegation-and-governance.md) — participate in community governance
- [See the full CLI command reference](../cli/command-reference.md) — all available commands and flags
