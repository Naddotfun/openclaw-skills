# INDEXER Skill - Historical Event Querying

Query past bonding curve trades and DEX swaps using viem's RPC-based indexing.

> **Setup**: See [SKILL.md](../SKILL.md) for network config and client setup. ABIs in [abi.md](./abi.md).

## Network Config

| Network | CURVE                                      | V3_FACTORY                                 | WMON                                       |
| ------- | ------------------------------------------ | ------------------------------------------ | ------------------------------------------ |
| Testnet | 0x1228b0dc9481C11D3071E7A924B794CfB038994e | 0xd0a37cf728CE2902eB8d4F6f2afc76854048253b | 0x5a4E0bFDeF88C9032CB4d24338C5EB3d3870BfDd |
| Mainnet | 0xA7283d07812a02AFB7C09B60f8896bCEA3F90aCE | 0x6B5F564339DbAD6b780249827f2198a841FEB7F3 | 0x3bd359C1119dA7Da1D913D1C4D2B7c461115433A |

## Basic Query

```typescript
const logs = await publicClient.getContractEvents({
  address: CURVE_ADDRESS, abi: curveAbi, eventName: "CurveBuy",
  fromBlock: 1000000n, toBlock: 1001000n, args: { token: tokenAddress },
})
```

## Curve Events

| Event              | Args                                                                       |
| ------------------ | -------------------------------------------------------------------------- |
| `CurveCreate`      | creator, token, pool, name, symbol, tokenURI, virtualMon, virtualToken     |
| `CurveBuy/Sell`    | sender, token, amountIn, amountOut                                         |
| `CurveSync`        | token, realMonReserve, realTokenReserve, virtualMonReserve, virtualTokenReserve |
| `CurveGraduate`    | token, pool                                                                |
| `CurveTokenLocked` | token                                                                      |

## DEX (Uniswap V3) Events

```typescript
// Discover pool
const pool = await publicClient.readContract({ address: V3_FACTORY, abi: uniswapV3FactoryAbi, functionName: "getPool", args: [tokenAddress, WMON_ADDRESS, 3000] })

// Query swaps
const swaps = await publicClient.getContractEvents({ address: poolAddress, abi: uniswapV3PoolAbi, eventName: "Swap", fromBlock, toBlock })
```

## Block Range Tips

```typescript
const latest = await publicClient.getBlockNumber()
const safeToBlock = latest - 10n // avoid reorgs
const oneHourAgo = latest - 3600n // ~1 block/sec on Monad

// Paginate large ranges
const CHUNK = 10000n
for (let from = start; from < end; from += CHUNK) {
  const to = from + CHUNK > end ? end : from + CHUNK
  const events = await publicClient.getContractEvents({ ...fromBlock: from, toBlock: to })
}
```

## Common Use Cases

| Task                    | Event               | Filter            |
| ----------------------- | ------------------- | ----------------- |
| All trades on token X   | CurveBuy, CurveSell | `args: { token }` |
| When did token graduate | CurveGraduate       | `args: { token }` |
| Recent token creations  | CurveCreate         | Last N blocks     |
