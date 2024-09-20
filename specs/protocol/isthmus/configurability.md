# Configurability

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [`SystemConfig`](#systemconfig)
  - [`ConfigUpdate`](#configupdate)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## `SystemConfig`

### `ConfigUpdate`

The following `ConfigUpdate` enum is defined where the `CONFIG_VERSION` is `uint256(1)`:

| Name | Value | Definition | Usage |
| ---- | ----- | --- | -- |
| `BATCHER` | `uint8(0)` | `abi.encode(address)` | Modifies the account that is authorized to progress the safe chain |
| `FEE_SCALARS` | `uint8(1)` | `(uint256(0x01) << 248) \| (uint256(_configurablefeescalar) << 128 \| (uint256(_configurablefeeconstant) << 64 \| (uint256(_blobbasefeeScalar) << 32) \| _basefeeScalar` | Modifies the fee scalars |
| `GAS_LIMIT` | `uint8(2)` | `abi.encode(uint64 _gasLimit)` | Modifies the L2 gas limit |
| `UNSAFE_BLOCK_SIGNER` | `uint8(3)` | `abi.encode(address)` | Modifies the account that is authorized to progress the unsafe chain |
| `EIP_1559_PARAMS` | `uint8(4)` | `uint256(uint64(_denominator)) << 32 \| uint64(_elasticity)` | Modifies the EIP-1559 denominator and elasticity |
