# NadFun ABIs - Smart Contract Interfaces

Comprehensive ABI reference for interacting with NadFun contracts using viem.

## Table of Contents

1. [Lens ABI](#lens-abi) - Quote and progress queries
2. [Bonding Curve ABI](#bonding-curve-abi) - Curve pool management
3. [Router ABI](#router-abi) - Trading
4. [Bonding Curve Router ABI](#bonding-curve-router-abi) - Token creation
5. [DEX Router ABI](#dex-router-abi) - DEX trading
6. [ERC20 Token ABI](#erc20-token-abi) - Token operations
7. [Creator Treasury ABI](#creator-treasury-abi) - Reward claims
8. [Uniswap V3 ABIs](#uniswap-v3-abis) - Pool discovery

---

## Lens ABI

```typescript
export const lensAbi = [
  {
    type: "function",
    name: "getAmountOut",
    inputs: [
      { name: "_token", type: "address" },
      { name: "_amountIn", type: "uint256" },
      { name: "_isBuy", type: "bool" },
    ],
    outputs: [
      { name: "router", type: "address" },
      { name: "amountOut", type: "uint256" },
    ],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "getAmountIn",
    inputs: [
      { name: "_token", type: "address" },
      { name: "_amountOut", type: "uint256" },
      { name: "_isBuy", type: "bool" },
    ],
    outputs: [
      { name: "router", type: "address" },
      { name: "amountIn", type: "uint256" },
    ],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "getProgress",
    inputs: [{ name: "_token", type: "address" }],
    outputs: [{ name: "progress", type: "uint256" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "getInitialBuyAmountOut",
    inputs: [{ name: "amountIn", type: "uint256" }],
    outputs: [{ type: "uint256" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "isGraduated",
    inputs: [{ name: "token", type: "address" }],
    outputs: [{ type: "bool" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "isLocked",
    inputs: [{ name: "token", type: "address" }],
    outputs: [{ type: "bool" }],
    stateMutability: "view",
  },
] as const;
```

---

## Bonding Curve ABI

```typescript
export const curveAbi = [
  {
    type: "function",
    name: "curves",
    inputs: [{ name: "token", type: "address" }],
    outputs: [
      { name: "realMonReserve", type: "uint256" },
      { name: "realTokenReserve", type: "uint256" },
      { name: "virtualMonReserve", type: "uint256" },
      { name: "virtualTokenReserve", type: "uint256" },
      { name: "k", type: "uint256" },
      { name: "targetTokenAmount", type: "uint256" },
      { name: "initVirtualMonReserve", type: "uint256" },
      { name: "initVirtualTokenReserve", type: "uint256" },
    ],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "feeConfig",
    inputs: [],
    outputs: [
      { name: "deployFeeAmount", type: "uint256" },
      { name: "graduateFeeAmount", type: "uint256" },
      { name: "protocolFee", type: "uint24" },
    ],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "isGraduated",
    inputs: [{ name: "token", type: "address" }],
    outputs: [{ type: "bool" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "isLocked",
    inputs: [{ name: "token", type: "address" }],
    outputs: [{ type: "bool" }],
    stateMutability: "view",
  },
  {
    type: "event",
    name: "CurveCreate",
    inputs: [
      { name: "creator", type: "address", indexed: true },
      { name: "token", type: "address", indexed: true },
      { name: "pool", type: "address", indexed: true },
      { name: "name", type: "string" },
      { name: "symbol", type: "string" },
      { name: "tokenURI", type: "string" },
      { name: "virtualMon", type: "uint256" },
      { name: "virtualToken", type: "uint256" },
      { name: "targetTokenAmount", type: "uint256" },
    ],
  },
  {
    type: "event",
    name: "CurveBuy",
    inputs: [
      { name: "sender", type: "address", indexed: true },
      { name: "token", type: "address", indexed: true },
      { name: "amountIn", type: "uint256" },
      { name: "amountOut", type: "uint256" },
    ],
  },
  {
    type: "event",
    name: "CurveSell",
    inputs: [
      { name: "sender", type: "address", indexed: true },
      { name: "token", type: "address", indexed: true },
      { name: "amountIn", type: "uint256" },
      { name: "amountOut", type: "uint256" },
    ],
  },
  {
    type: "event",
    name: "CurveGraduate",
    inputs: [
      { name: "token", type: "address", indexed: true },
      { name: "pool", type: "address", indexed: true },
    ],
  },
] as const;
```

---

## Router ABI

```typescript
export const routerAbi = [
  {
    type: "function",
    name: "buy",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "payable",
  },
  {
    type: "function",
    name: "sell",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountIn", type: "uint256" },
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "nonpayable",
  },
  {
    type: "function",
    name: "sellPermit",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountIn", type: "uint256" },
          { name: "amountOutMin", type: "uint256" },
          { name: "amountAllowance", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
          { name: "v", type: "uint8" },
          { name: "r", type: "bytes32" },
          { name: "s", type: "bytes32" },
        ],
      },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "nonpayable",
  },
] as const;
```

---

## Bonding Curve Router ABI

```typescript
export const bondingCurveRouterAbi = [
  {
    type: "function",
    name: "create",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "name", type: "string" },
          { name: "symbol", type: "string" },
          { name: "tokenURI", type: "string" },
          { name: "amountOut", type: "uint256" },
          { name: "salt", type: "bytes32" },
          { name: "actionId", type: "uint8" },
        ],
      },
    ],
    outputs: [
      { name: "token", type: "address" },
      { name: "pool", type: "address" },
    ],
    stateMutability: "payable",
  },
  {
    type: "function",
    name: "buy",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [],
    stateMutability: "payable",
  },
  {
    type: "function",
    name: "sell",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountIn", type: "uint256" },
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [],
    stateMutability: "nonpayable",
  },
  {
    type: "function",
    name: "sellPermit",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountIn", type: "uint256" },
          { name: "amountOutMin", type: "uint256" },
          { name: "amountAllowance", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
          { name: "v", type: "uint8" },
          { name: "r", type: "bytes32" },
          { name: "s", type: "bytes32" },
        ],
      },
    ],
    outputs: [],
    stateMutability: "nonpayable",
  },
] as const;
```

---

## DEX Router ABI

```typescript
export const dexRouterAbi = [
  {
    type: "function",
    name: "buy",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "payable",
  },
  {
    type: "function",
    name: "sell",
    inputs: [
      {
        name: "params",
        type: "tuple",
        components: [
          { name: "amountIn", type: "uint256" },
          { name: "amountOutMin", type: "uint256" },
          { name: "token", type: "address" },
          { name: "to", type: "address" },
          { name: "deadline", type: "uint256" },
        ],
      },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "nonpayable",
  },
  {
    type: "function",
    name: "getAmountOut",
    inputs: [
      { name: "token", type: "address" },
      { name: "amountIn", type: "uint256" },
      { name: "isBuy", type: "bool" },
    ],
    outputs: [{ name: "amountOut", type: "uint256" }],
    stateMutability: "view",
  },
] as const;
```

---

## ERC20 Token ABI

```typescript
export const erc20Abi = [
  {
    type: "function",
    name: "transfer",
    inputs: [
      { name: "to", type: "address" },
      { name: "amount", type: "uint256" },
    ],
    outputs: [{ type: "bool" }],
    stateMutability: "nonpayable",
  },
  {
    type: "function",
    name: "approve",
    inputs: [
      { name: "spender", type: "address" },
      { name: "amount", type: "uint256" },
    ],
    outputs: [{ type: "bool" }],
    stateMutability: "nonpayable",
  },
  {
    type: "function",
    name: "balanceOf",
    inputs: [{ name: "account", type: "address" }],
    outputs: [{ type: "uint256" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "allowance",
    inputs: [
      { name: "owner", type: "address" },
      { name: "spender", type: "address" },
    ],
    outputs: [{ type: "uint256" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "nonces",
    inputs: [{ name: "owner", type: "address" }],
    outputs: [{ type: "uint256" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "name",
    inputs: [],
    outputs: [{ type: "string" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "symbol",
    inputs: [],
    outputs: [{ type: "string" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "decimals",
    inputs: [],
    outputs: [{ type: "uint8" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "totalSupply",
    inputs: [],
    outputs: [{ type: "uint256" }],
    stateMutability: "view",
  },
] as const;
```

---

## Creator Treasury ABI

```typescript
export const creatorTreasuryAbi = [
  {
    type: "function",
    name: "claim",
    inputs: [
      { name: "tokens", type: "address[]" },
      { name: "amounts", type: "uint256[]" },
      { name: "merkleProofs", type: "bytes32[][]" },
    ],
    outputs: [],
    stateMutability: "nonpayable",
  },
] as const;
```

---

## Uniswap V3 ABIs

```typescript
export const uniswapV3PoolAbi = [
  {
    type: "event",
    name: "Swap",
    inputs: [
      { name: "sender", type: "address", indexed: true },
      { name: "recipient", type: "address", indexed: true },
      { name: "amount0", type: "int256" },
      { name: "amount1", type: "int256" },
      { name: "sqrtPriceX96", type: "uint160" },
      { name: "liquidity", type: "uint128" },
      { name: "tick", type: "int24" },
    ],
  },
  {
    type: "function",
    name: "token0",
    inputs: [],
    outputs: [{ type: "address" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "token1",
    inputs: [],
    outputs: [{ type: "address" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "fee",
    inputs: [],
    outputs: [{ type: "uint24" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "liquidity",
    inputs: [],
    outputs: [{ type: "uint128" }],
    stateMutability: "view",
  },
  {
    type: "function",
    name: "slot0",
    inputs: [],
    outputs: [
      { name: "sqrtPriceX96", type: "uint160" },
      { name: "tick", type: "int24" },
      { name: "observationIndex", type: "uint16" },
      { name: "observationCardinality", type: "uint16" },
      { name: "observationCardinalityNext", type: "uint16" },
      { name: "feeProtocol", type: "uint8" },
      { name: "unlocked", type: "bool" },
    ],
    stateMutability: "view",
  },
] as const;

export const uniswapV3FactoryAbi = [
  {
    type: "function",
    name: "getPool",
    inputs: [
      { name: "tokenA", type: "address" },
      { name: "tokenB", type: "address" },
      { name: "fee", type: "uint24" },
    ],
    outputs: [{ name: "pool", type: "address" }],
    stateMutability: "view",
  },
] as const;
```
