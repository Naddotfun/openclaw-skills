# TOKEN Skill - ERC-20 Operations

Low-level token operations with viem for ERC-20 and ERC-2612 permit support.

> **Setup**: See [SKILL.md](../SKILL.md) for network config and client setup. ABIs in [abi.md](./abi.md).

## 1. Get Balance

```typescript
const balance = await publicClient.readContract({ address: tokenAddress, abi: erc20Abi, functionName: "balanceOf", args: [ownerAddress] })
```

## 2. Get Metadata (Multicall)

```typescript
const results = await publicClient.multicall({
  contracts: [
    { address: tokenAddress, abi: erc20Abi, functionName: "name" },
    { address: tokenAddress, abi: erc20Abi, functionName: "symbol" },
    { address: tokenAddress, abi: erc20Abi, functionName: "decimals" },
    { address: tokenAddress, abi: erc20Abi, functionName: "totalSupply" },
  ],
})
```

## 3. Allowance / Approve / Transfer

```typescript
const allowance = await publicClient.readContract({ address: tokenAddress, abi: erc20Abi, functionName: "allowance", args: [ownerAddress, spenderAddress] })

await walletClient.writeContract({ address: tokenAddress, abi: erc20Abi, functionName: "approve", args: [spenderAddress, amount], account, chain })

await walletClient.writeContract({ address: tokenAddress, abi: erc20Abi, functionName: "transfer", args: [toAddress, amount], account, chain })
```

## 4. Permit Signature (ERC-2612)

```typescript
const nonce = await publicClient.readContract({
  address: tokenAddress,
  abi: [{ name: "nonces", type: "function", stateMutability: "view", inputs: [{ name: "owner", type: "address" }], outputs: [{ type: "uint256" }] }],
  functionName: "nonces", args: [ownerAddress],
})

const tokenName = await publicClient.readContract({ address: tokenAddress, abi: erc20Abi, functionName: "name" })

const signature = await walletClient.signTypedData({
  account,
  domain: { name: tokenName, version: "1", chainId: chain.id, verifyingContract: tokenAddress },
  types: { Permit: [{ name: "owner", type: "address" }, { name: "spender", type: "address" }, { name: "value", type: "uint256" }, { name: "nonce", type: "uint256" }, { name: "deadline", type: "uint256" }] },
  primaryType: "Permit",
  message: { owner: ownerAddress, spender, value, nonce, deadline },
})

const r = `0x${signature.slice(2, 66)}` as `0x${string}`
const s = `0x${signature.slice(66, 130)}` as `0x${string}`
const v = parseInt(signature.slice(130, 132), 16) as 27 | 28
```

## Key Points

- **All amounts in raw bigint** - multiply/divide by `10**decimals` for display
- **Permit requires ERC-2612** - verify token has `nonces` function
- **Nonce must be fresh** - fetch immediately before signing
- **All NadFun tokens use 18 decimals**
