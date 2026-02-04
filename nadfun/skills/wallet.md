# WALLET Skill - Key & Address Generation

Wallet generation using viem.

## Generate New Wallet

```typescript
import { generatePrivateKey, privateKeyToAccount } from "viem/accounts"

const privateKey = generatePrivateKey() // '0x...' (64 hex chars)
const account = privateKeyToAccount(privateKey)
// account.address, account.publicKey
```

## Import Existing Key

```typescript
const account = privateKeyToAccount("0x..." as `0x${string}`)
```

## Create Wallet Client

```typescript
import { createWalletClient, http } from "viem"

const walletClient = createWalletClient({
  account,
  chain, // from SKILL.md setup
  transport: http(CONFIG.rpcUrl),
})
```

## Signing Methods

```typescript
account.signMessage({ message: 'Hello' })
account.signTransaction({ ... })
account.signTypedData({ ... })
```

## Security

- **Never hardcode** private keys
- Use environment variables: `process.env.PRIVATE_KEY as \`0x\${string}\``
- Don't log private keys in production
