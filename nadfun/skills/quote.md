# QUOTE Skill - Price Quotes and Curve State

Pure viem implementation for querying bonding curve prices and state.

> **Setup**: See [SKILL.md](../SKILL.md) for network config and client setup. ABIs in [abi.md](./abi.md).

## 1. getAmountOut

Get expected output amount for buy/sell.

```typescript
const [router, amountOut] = await publicClient.readContract({
  address: CONFIG.LENS, abi: lensAbi, functionName: "getAmountOut",
  args: [tokenAddress, amountIn, isBuy], // isBuy: true=buying tokens, false=selling
})
```

## 2. getAmountIn

Get required input for desired output.

```typescript
const [router, amountIn] = await publicClient.readContract({
  address: CONFIG.LENS, abi: lensAbi, functionName: "getAmountIn",
  args: [tokenAddress, amountOut, isBuy],
})
```

## 3. getCurveState

Get complete bonding curve state.

```typescript
const [realMonReserve, realTokenReserve, virtualMonReserve, virtualTokenReserve, k, targetTokenAmount, initVirtualMonReserve, initVirtualTokenReserve] =
  await publicClient.readContract({ address: CONFIG.CURVE, abi: curveAbi, functionName: "curves", args: [tokenAddress] })
```

## 4. getProgress

Get graduation progress (0-10000 = 0-100%).

```typescript
const progress = await publicClient.readContract({ address: CONFIG.LENS, abi: lensAbi, functionName: "getProgress", args: [tokenAddress] })
const percentage = Number(progress) / 100
```

## 5. isGraduated / isLocked

```typescript
const graduated = await publicClient.readContract({ address: CONFIG.CURVE, abi: curveAbi, functionName: "isGraduated", args: [tokenAddress] })
const locked = await publicClient.readContract({ address: CONFIG.CURVE, abi: curveAbi, functionName: "isLocked", args: [tokenAddress] })
const canTrade = !graduated && !locked
```

## Network Details

| Network | Chain ID | LENS                                       | CURVE                                      |
| ------- | -------- | ------------------------------------------ | ------------------------------------------ |
| Testnet | 10143    | 0xB056d79CA5257589692699a46623F901a3BB76f1 | 0x1228b0dc9481C11D3071E7A924B794CfB038994e |
| Mainnet | 143      | 0x7e78A8DE94f21804F7a17F4E8BF9EC2c872187ea | 0xA7283d07812a02AFB7C09B60f8896bCEA3F90aCE |
