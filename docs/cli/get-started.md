# Get Started with the CLI

In this tutorial, we will install the Sage CLI, connect a wallet, and create a prompt library with a personal DAO. Along the way, we will use the CLI's quickstart workflow, upload prompts to IPFS, and publish updates to an existing library.

## Prerequisites

- **Node.js** v18 or later (`node --version` to check)
- **npm** v8 or later
- A web3 wallet (we will use the Privy browser-based login)

## Step 1: Install the CLI

```bash
npm i -g @sage-protocol/cli
```

Verify the installation:

```bash
sage --version
```

You should see output similar to:

```
@sage-protocol/cli@0.6.2
```

The CLI binary is called `sage`. It will check for updates once per day automatically.

## Step 2: Connect your wallet

We will use the interactive setup wizard to connect a wallet.

```bash
sage wizard
```

Follow the prompts in your terminal. The wizard opens a browser window for Privy-based wallet login on Base Sepolia.

Once complete, verify the connection:

```bash
sage wallet doctor
```

You should see output showing your wallet address, chain ID `84532`, and a green checkmark for RPC connectivity.

## Step 3: Get testnet tokens

We need SXXX governance tokens to create a DAO.

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

You should see at least 5000 SXXX. (See [the wallet reference](./command-reference.md) for all wallet-related commands.)

## Step 4: Create a prompt directory

Let's create a directory with a prompt file for our library.

```bash
mkdir prompts
```

```bash
echo "# My First Prompt

Your prompt content here" > prompts/my-prompt.md
```

## Step 5: Create a library

Now we will use `library quickstart` to create a DAO, upload our prompt to IPFS, and register everything on-chain in one command.

```bash
sage library quickstart --name "My Library" --from-dir ./prompts
```

You should see output similar to:

```
✓ Scanned 1 prompt from ./prompts
✓ Uploaded to IPFS
✓ Created DAO at 0xAbC123... (personal governance)
✓ Manifest CID: bafybeig...
✓ Registered on-chain
✓ Saved alias "My Library"
✓ Context set to "My Library"
```

Notice that the CLI created a personal governance DAO, saved an alias, and set it as our active context. All future commands will use this DAO by default. (See [Governance Modes](../core-concepts/governance-modes.md) for other governance types.)

## Step 6: Add another prompt

Let's add a second prompt to our library.

```bash
sage prompts new --name "another-prompt"
```

You should see:

```
✓ Created prompts/another-prompt.md
```

Edit `prompts/another-prompt.md` in your editor and add your content, then save.

## Step 7: Publish the update

```bash
sage prompts publish --yes
```

You should see output similar to:

```
✓ Uploaded 2 prompts to IPFS
✓ Manifest CID: bafybeih...
✓ Proposal created and executed
✓ Library updated on-chain
```

Notice that the new manifest includes both our original and new prompt.

## What we accomplished

We installed the Sage CLI, connected a wallet via Privy, obtained testnet SXXX, and created a governed prompt library on Base Sepolia. We then added a second prompt and published an update. From here, you can:

- [Create a SubDAO with custom governance](../guides/creating-a-subdao.md) — explore community and council governance
- [Publish and version prompts](../guides/publishing-and-versioning-prompts.md) — advanced prompt management workflows
- [Set up the MCP server for AI agents](../guides/using-the-mcp-server.md) — let agents access your library
- [See the full CLI command reference](./command-reference.md) — all available commands and flags
