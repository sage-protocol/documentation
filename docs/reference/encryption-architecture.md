# Encryption Architecture

This document provides a technical deep-dive into how Sage Protocol encrypts and decrypts premium prompt content using a hybrid encryption model combining AES-256-GCM and Lit Protocol.

---

## Overview

Sage uses **hybrid encryption** for premium prompts:

| Layer              | Technology   | Purpose                                               |
| ------------------ | ------------ | ----------------------------------------------------- |
| Content encryption | AES-256-GCM  | Fast symmetric encryption for prompt content          |
| Key management     | Lit Protocol | Decentralized key gating with on-chain access control |
| Access control     | ERC1155      | License receipts prove purchase ownership             |

This architecture ensures:

- Content is encrypted at rest on IPFS
- No central server holds decryption keys
- License ownership is on-chain and verifiable
- Creators don't need to be online for buyers to decrypt

---

## Why Hybrid Encryption?

**Direct Lit encryption** would encrypt the entire content with Lit nodes. This works but has limitations:

- Slower for large content (network round-trips per decrypt)
- Higher costs for repeated access
- Content size limits

**Hybrid approach** solves these:

1. Content encrypted locally with AES-256-GCM (fast, no size limits)
2. Only the small symmetric key (32 bytes) is encrypted with Lit
3. Lit enforces the on-chain access condition
4. Decryption is fast after key retrieval

---

## Encryption Flow (Publishing)

When a creator publishes a premium prompt:

```
┌─────────────────────────────────────────────────────────────────┐
│ Step 1: Generate Symmetric Key                                  │
│                                                                 │
│   symmetricKey = crypto.randomBytes(32)  // 256 bits            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 2: Encrypt Content with AES-256-GCM                        │
│                                                                 │
│   const { ciphertext, iv, tag } = encryptAesGcm(                │
│     promptContent,   // plaintext string                        │
│     symmetricKey     // 32-byte key                             │
│   );                                                            │
│                                                                 │
│   // iv: 12-byte initialization vector                          │
│   // tag: 16-byte authentication tag                            │
│   // ciphertext: encrypted content                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 3: Upload Encrypted Payload to IPFS                        │
│                                                                 │
│   encryptedPayload = {                                          │
│     type: "sage-personal-encrypted",                            │
│     enc: "aes-256-gcm",                                         │
│     name: "My Premium Prompt",                                  │
│     description: "...",                                         │
│     iv: toBase64(iv),                                           │
│     tag: toBase64(tag),                                         │
│     ciphertext: toBase64(ciphertext)                            │
│   }                                                             │
│                                                                 │
│   encryptedCid = await ipfs.upload(encryptedPayload)            │
│   // e.g., "bafkreig..."                                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 4: Compute Receipt ID                                      │
│                                                                 │
│   // Deterministic ID from creator address and prompt key       │
│   keyHash = keccak256(promptKey)                                │
│   receiptId = keccak256(abi.encode(creatorAddress, keyHash))    │
│                                                                 │
│   // This ID will be the ERC1155 token ID for license receipts  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 5: Build Lit Access Control Conditions                     │
│                                                                 │
│   accessConditions = [{                                         │
│     conditionType: "evmContract",                               │
│     contractAddress: PERSONAL_LICENSE_RECEIPT_ADDRESS,          │
│     functionName: "balanceOf",                                  │
│     functionParams: [":userAddress", receiptId.toString()],     │
│     functionAbi: {                                              │
│       inputs: [                                                 │
│         { name: "account", type: "address" },                   │
│         { name: "id", type: "uint256" }                         │
│       ],                                                        │
│       name: "balanceOf",                                        │
│       outputs: [{ type: "uint256" }],                           │
│       stateMutability: "view",                                  │
│       type: "function"                                          │
│     },                                                          │
│     chain: "baseSepolia",                                       │
│     returnValueTest: {                                          │
│       comparator: ">",                                          │
│       value: "0"                                                │
│     }                                                           │
│   }]                                                            │
│                                                                 │
│   // Meaning: "Only decrypt if user owns at least 1 receipt"    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 6: Encrypt Symmetric Key with Lit Protocol                 │
│                                                                 │
│   const client = new LitNodeClient({                            │
│     litNetwork: "datil-test"                                    │
│   });                                                           │
│   await client.connect();                                       │
│                                                                 │
│   // Generate auth signature (wallet signs SIWE message)        │
│   authSig = await buildNodeAuthSig(wallet, chain);              │
│                                                                 │
│   // Encrypt the symmetric key                                  │
│   const { ciphertext, dataToEncryptHash } = await               │
│     encryptUint8Array({                                         │
│       evmContractConditions: accessConditions,                  │
│       chain: "baseSepolia",                                     │
│       dataToEncrypt: Uint8Array.from(symmetricKey),             │
│       authSig                                                   │
│     }, client);                                                 │
│                                                                 │
│   // Lit nodes now hold shards of the encryption key            │
│   // ciphertext: encrypted symmetric key (base64)               │
│   // dataToEncryptHash: verification hash                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 7: Create and Upload Manifest                              │
│                                                                 │
│   manifest = {                                                  │
│     type: "sage-personal-manifest",                             │
│     version: "1.0.0",                                           │
│     key: "trading-strategy",                                    │
│     name: "Advanced Trading Strategy",                          │
│     description: "Professional trading analysis...",            │
│     creator: "0xCreator...",                                    │
│     receiptId: "12345...",                                      │
│     receiptIdHex: "0x3039...",                                  │
│     price: "25",                                                │
│     encryptedCid: "bafkreig...",                                │
│     lit: {                                                      │
│       chain: "baseSepolia",                                     │
│       receiptAddress: "0xReceipt...",                           │
│       receiptId: "12345...",                                    │
│       evmContractConditions: accessConditions,                  │
│       encryptedSymmetricKey: ciphertext,                        │
│       dataToEncryptHash: dataToEncryptHash                      │
│     }                                                           │
│   }                                                             │
│                                                                 │
│   manifestCid = await ipfs.upload(manifest)                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 8: Register On-Chain                                       │
│                                                                 │
│   // Set price in PersonalMarketplace                           │
│   keyHash = keccak256(promptKey)                                │
│   await marketplace.setPrice(keyHash, priceInWei)               │
│                                                                 │
│   // Optionally register in PersonalRegistry                    │
│   await registry.addPrompt({                                    │
│     title, key, contentCID, metadataCID, tags, category         │
│   })                                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Purchase Flow

When a buyer purchases a premium prompt:

```
┌─────────────────────────────────────────────────────────────────┐
│ 1. Buyer calls PersonalMarketplace.buy(creator, keyHash)        │
│    - Transfers SXXX from buyer to creator (minus protocol fee)  │
│    - Protocol fee (10%) goes to protocol treasury               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 2. PersonalLicenseReceipt mints ERC1155 token                   │
│    - Token ID = receiptId                                       │
│    - balanceOf(buyer, receiptId) = 1                            │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 3. LicensePurchased event emitted                               │
│    - Indexed by subgraph for discovery                          │
│    - Contains: creator, buyer, key, encryptedCid, metadata      │
└─────────────────────────────────────────────────────────────────┘
```

---

## Decryption Flow (Access)

When a license holder accesses purchased content:

```
┌─────────────────────────────────────────────────────────────────┐
│ Step 1: Verify License Ownership                                │
│                                                                 │
│   receiptId = computeReceiptId(creator, key)                    │
│   balance = await licenseReceipt.balanceOf(holder, receiptId)   │
│                                                                 │
│   if (balance === 0) throw Error("No license owned")            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 2: Resolve Encrypted Resource                              │
│                                                                 │
│   // Query subgraph or on-chain for CIDs                        │
│   { encryptedCid, manifestCid } = await resolveResource({       │
│     creator, key, subgraphUrl                                   │
│   })                                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 3: Fetch Manifest from IPFS                                │
│                                                                 │
│   manifest = await fetch(`${gateway}/${manifestCid}`)           │
│                                                                 │
│   // Extract Lit parameters                                     │
│   const { encryptedSymmetricKey, dataToEncryptHash,             │
│           evmContractConditions } = manifest.lit                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 4: Connect to Lit Network                                  │
│                                                                 │
│   const client = new LitNodeClient({                            │
│     litNetwork: "datil-test"                                    │
│   });                                                           │
│   await client.connect();                                       │
│                                                                 │
│   // Generate auth signature from wallet                        │
│   authSig = await buildNodeAuthSig(wallet, chain);              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 5: Request Decryption from Lit Nodes                       │
│                                                                 │
│   const response = await client.decrypt({                       │
│     unifiedAccessControlConditions: evmContractConditions,      │
│     chain: "baseSepolia",                                       │
│     ciphertext: encryptedSymmetricKey,                          │
│     dataToEncryptHash: dataToEncryptHash,                       │
│     authSig                                                     │
│   });                                                           │
│                                                                 │
│   // Lit nodes:                                                 │
│   // 1. Verify authSig (proves wallet ownership)                │
│   // 2. Check on-chain: balanceOf(userAddress, receiptId) > 0   │
│   // 3. If valid: combine key shards and return symmetric key   │
│   // 4. If invalid: reject with access denied error             │
│                                                                 │
│   symmetricKey = response.decryptedData  // Uint8Array          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 6: Fetch Encrypted Payload from IPFS                       │
│                                                                 │
│   encryptedPayload = await fetch(`${gateway}/${encryptedCid}`)  │
│                                                                 │
│   // Parse the payload                                          │
│   const { iv, tag, ciphertext } = encryptedPayload              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 7: Decrypt Content Locally with AES-256-GCM                │
│                                                                 │
│   plaintext = decryptAesGcm(                                    │
│     fromBase64(ciphertext),                                     │
│     symmetricKey,                                               │
│     fromBase64(iv),                                             │
│     fromBase64(tag)                                             │
│   );                                                            │
│                                                                 │
│   // plaintext contains the original prompt content             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Data Structures

### Encrypted Payload (IPFS)

Stored at `encryptedCid`:

```json
{
  "type": "sage-personal-encrypted",
  "enc": "aes-256-gcm",
  "name": "Advanced Trading Strategy",
  "description": "Professional trading analysis with risk management",
  "iv": "base64-encoded-12-bytes",
  "tag": "base64-encoded-16-bytes",
  "ciphertext": "base64-encoded-encrypted-content"
}
```

### Manifest (IPFS)

Stored at `manifestCid`:

```json
{
  "type": "sage-personal-manifest",
  "version": "1.0.0",
  "key": "trading-strategy",
  "name": "Advanced Trading Strategy",
  "description": "Professional trading analysis with risk management",
  "creator": "0x9794415Dc13cBF98d4B9f627dbd8Ba5968C507CA",
  "receiptId": "12345678901234567890",
  "receiptIdHex": "0xab54a98ceb1f0ad2",
  "price": "25",
  "encryptedCid": "bafkreig...",
  "lit": {
    "chain": "baseSepolia",
    "receiptAddress": "0xPersonalLicenseReceipt...",
    "receiptId": "12345678901234567890",
    "evmContractConditions": [
      {
        "conditionType": "evmContract",
        "contractAddress": "0xPersonalLicenseReceipt...",
        "functionName": "balanceOf",
        "functionParams": [":userAddress", "12345678901234567890"],
        "functionAbi": { "...": "..." },
        "chain": "baseSepolia",
        "returnValueTest": { "comparator": ">", "value": "0" }
      }
    ],
    "encryptedSymmetricKey": "base64-encoded-lit-ciphertext",
    "dataToEncryptHash": "0xhash..."
  },
  "metadata": {
    "custom": "fields"
  }
}
```

### Access Control Condition

The condition that Lit nodes evaluate:

```javascript
{
  conditionType: "evmContract",
  contractAddress: "0x...PersonalLicenseReceipt",
  functionName: "balanceOf",
  functionParams: [
    ":userAddress",      // Replaced with authenticated user's address
    "12345..."           // Receipt ID (ERC1155 token ID)
  ],
  functionAbi: {
    inputs: [
      { internalType: "address", name: "account", type: "address" },
      { internalType: "uint256", name: "id", type: "uint256" }
    ],
    name: "balanceOf",
    outputs: [{ internalType: "uint256", name: "", type: "uint256" }],
    stateMutability: "view",
    type: "function"
  },
  chain: "baseSepolia",
  returnValueTest: {
    comparator: ">",
    value: "0"
  }
}
```

**Meaning:** Lit will only release the decryption key if `balanceOf(userAddress, receiptId) > 0` returns true on-chain.

---

## Receipt ID Computation

Receipt IDs are **deterministic** and can be computed off-chain:

```javascript
const { keccak256, id, AbiCoder } = require("ethers");

function computeReceiptId(creatorAddress, promptKey) {
  const keyHash = id(promptKey); // keccak256(promptKey)
  const abiCoder = new AbiCoder();
  const encoded = abiCoder.encode(
    ["address", "bytes32"],
    [creatorAddress, keyHash],
  );
  return BigInt(keccak256(encoded));
}

// Example
const receiptId = computeReceiptId(
  "0x9794415Dc13cBF98d4B9f627dbd8Ba5968C507CA",
  "trading-strategy",
);
// Returns: 12345678901234567890... (BigInt)
```

This allows:

- Buyers to know the receipt ID before purchasing
- Third parties to verify license ownership
- Consistent IDs across all systems

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Lit Network                             │
│                        (datil-test)                             │
│                                                                 │
│   Holds shards of symmetric keys                                │
│   Verifies on-chain access conditions                           │
│   Releases keys only to authorized wallets                      │
└─────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│     IPFS      │     │  Base Sepolia │     │   Subgraph    │
│               │     │   (on-chain)  │     │   (indexer)   │
│               │     │               │     │               │
│ encryptedCid: │     │ Personal-     │     │ license-      │
│ - type        │     │ Marketplace   │     │ Purchases     │
│ - enc         │     │ - prices()    │     │               │
│ - iv, tag     │     │ - buy()       │     │ personal-     │
│ - ciphertext  │     │               │     │ Resources     │
│               │     │ Personal-     │     │               │
│ manifestCid:  │     │ LicenseReceipt│     │ Stores:       │
│ - lit params  │     │ - balanceOf() │     │ - encryptedCid│
│ - metadata    │     │ - mint()      │     │ - metadata    │
│               │     │               │     │ - receiptId   │
└───────────────┘     └───────────────┘     └───────────────┘
```

---

## Lit Network Configuration

Sage currently uses the **datil-test** network:

| Network      | Purpose           | Chain Support         |
| ------------ | ----------------- | --------------------- |
| `datil-test` | Testnet (default) | Base Sepolia, Sepolia |
| `datil-dev`  | Development       | Base Sepolia, Sepolia |
| `datil`      | Mainnet (future)  | Base, Ethereum        |

Configure via CLI:

```bash
sage personal premium publish my-prompt \
  --file ./prompt.md \
  --price 25 \
  --lit-network datil-test
```

Or environment variable:

```bash
export LIT_NETWORK=datil-test
```

---

## Security Properties

| Property                    | How It's Achieved                                                |
| --------------------------- | ---------------------------------------------------------------- |
| **Content confidentiality** | AES-256-GCM encryption with random key                           |
| **Key security**            | Lit Protocol threshold cryptography (no single point of failure) |
| **Access control**          | On-chain ERC1155 balance check                                   |
| **Tamper resistance**       | Content-addressed storage (CIDs)                                 |
| **Creator sovereignty**     | Creator doesn't need to be online                                |
| **License transferability** | ERC1155 tokens can be transferred                                |

---

## Implementation Files

| Component         | File                                              | Purpose                                    |
| ----------------- | ------------------------------------------------- | ------------------------------------------ |
| Encryption flow   | `packages/sdk/src/personal/*`                     | Personal encryption + access helpers       |
| Decryption flow   | `packages/sdk/src/personal/*`                     | Personal decryption + access helpers       |
| Access conditions | `packages/sage-web-app/src/lib/sage/premium.ts`    | `buildAccessControlConditions`             |
| SDK decryption    | `packages/sdk/src/personal/access.js:129-185`     | `decryptWithLit()`                         |
| Receipt ID        | `packages/sdk/src/personal/receipt.js`            | `computeReceiptId()`                       |

---

## Related

- [Premium Prompts](../concepts/premium-prompts.md) - Conceptual overview
- [Creating and Selling Premium Prompts](../guides/creating-and-selling-premium-prompts.md) - User guide
- [Contracts Architecture](../contracts/architecture.md) - On-chain components
