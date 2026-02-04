# AUSD Skill - Swap & Yield

Get aUSD via LiFi swap, then deposit to Upshift Finance vault for yield.

> **Setup**: See [SKILL.md](../SKILL.md) for network config and client setup.

## Contract Addresses (Mainnet Only)

| Contract    | Address                                    |
| ----------- | ------------------------------------------ |
| LiFi Router | 0x026F252016A7C47CDEf1F05a3Fc9E20C92a49C37 |
| aUSD        | 0x00000000eFE302BEAA2b3e6e1b18d08D69a9012a |
| Vault       | 0x36eDbF0C834591BFdfCaC0Ef9605528c75c406aA |

## Part 1: LiFi Swap (MON → aUSD)

```typescript
const LIFI_API = "https://li.quest/v1"

async function getLiFiQuote(fromAddress: string, amountWei: bigint) {
  const params = new URLSearchParams({
    fromChain: "143", toChain: "143",
    fromToken: "0x0000000000000000000000000000000000000000",
    toToken: "0x00000000eFE302BEAA2b3e6e1b18d08D69a9012a",
    fromAmount: amountWei.toString(), fromAddress, toAddress: fromAddress,
    slippage: "0.03", integrator: "NadFun", fee: "0.01",
  })
  return fetch(`${LIFI_API}/quote?${params}`).then(r => r.json())
}

// Execute swap
const quote = await getLiFiQuote(account.address, parseEther("0.1"))
const txHash = await walletClient.sendTransaction({
  to: quote.transactionRequest.to,
  data: quote.transactionRequest.data,
  value: BigInt(quote.transactionRequest.value),
  gas: BigInt(quote.transactionRequest.gasLimit),
})
```

## Part 2: Upshift Vault (aUSD → earnAUSD)

```typescript
// Check vault status
const depositsPaused = await publicClient.readContract({ address: VAULT, abi: vaultAbi, functionName: "depositsPaused" })

// Approve & deposit (aUSD has 6 decimals!)
const depositAmount = parseUnits("100", 6)
await walletClient.writeContract({ address: AUSD, abi: erc20Abi, functionName: "approve", args: [VAULT, depositAmount], account, chain })
await walletClient.writeContract({ address: VAULT, abi: vaultAbi, functionName: "deposit", args: [AUSD, depositAmount, account.address], account, chain })
```

## Redeem Options

| Method         | Fee      | Wait Time |
| -------------- | -------- | --------- |
| Instant Redeem | **0.2%** | None      |
| Request Redeem | **None** | ~3 days   |

```typescript
// Instant redeem (0.2% fee)
await walletClient.writeContract({ address: VAULT, abi: vaultAbi, functionName: "instantRedeem", args: [shares, account.address], account, chain })

// Request redeem (no fee, ~3 day wait)
const lpToken = await publicClient.readContract({ address: VAULT, abi: vaultAbi, functionName: "lpTokenAddress" })
await walletClient.writeContract({ address: lpToken, abi: erc20Abi, functionName: "approve", args: [VAULT, shares], account, chain })
await walletClient.writeContract({ address: VAULT, abi: vaultAbi, functionName: "requestRedeem", args: [shares, account.address], account, chain })
// Wait ~3 days, then claim
await walletClient.writeContract({ address: VAULT, abi: vaultAbi, functionName: "claim", args: [year, month, day, account.address], account, chain })
```

## Key Points

| Topic        | Note                                           |
| ------------ | ---------------------------------------------- |
| **Decimals** | MON = 18, aUSD = 6, earnAUSD = 6               |
| **LiFi**     | Both `integrator` + `fee` required for revenue |
| **Vault**    | Always check `depositsPaused` before deposit   |
