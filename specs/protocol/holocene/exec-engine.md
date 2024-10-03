# L2 Execution Engine

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Overview](#overview)
- [Timestamp Activation](#timestamp-activation)
- [Dynamic EIP-1559 Parameters](#dynamic-eip-1559-parameters)
  - [Extended `PayloadAttributesV3`](#extended-payloadattributesv3)
    - [`eip1559Params` encoding](#eip1559params-encoding)
  - [Execution](#execution)
  - [Rationale](#rationale)
  - [`eip1559Params` in Header](#eip1559params-in-header)
    - [Header Validity Rules](#header-validity-rules)
    - [Encoding](#encoding)
    - [Rationale](#rationale-1)
- [Fees](#fees)
  - [Operator Fee](#operator-fee)
    - [Configuring Scalars](#configuring-scalars)
  - [Fee Vaults](#fee-vaults)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview

The EIP-1559 parameters are encoded in the block header's `nonce` field and can be
configured dynamically through the `SystemConfig`.

## Timestamp Activation

Holocene, like other network upgrades, is activated at a timestamp.
Changes to the L2 Block execution rules are applied when the `L2 Timestamp >= activation time`.

## Dynamic EIP-1559 Parameters

### Extended `PayloadAttributesV3`

The [`PayloadAttributesV3`](https://github.com/ethereum/execution-apis/blob/cea7eeb642052f4c2e03449dc48296def4aafc24/src/engine/cancun.md#payloadattributesv3)
type is extended to:

```rs
PayloadAttributesV3: {
    timestamp: QUANTITY
    random: DATA (32 bytes)
    suggestedFeeRecipient: DATA (20 bytes)
    withdrawals: array of WithdrawalV1
    parentBeaconBlockRoot: DATA (32 bytes)
    transactions: array of DATA
    noTxPool: bool
    gasLimit: QUANTITY or null
    eip1559Params: DATA (8 bytes)
}
```

#### `eip1559Params` encoding

| Name          | Type               | Byte Offset |
| ------------- | ------------------ | ----------- |
| `denominator` | `u32 (big-endian)` | `[0, 4)`    |
| `elasticity`  | `u32 (big-endian)` | `[4, 8)`    |

### Execution

During execution, the EIP-1559 parameters used to calculate the next block base fee should come from the
`PayloadAttributesV3` type rather than the previous protocol constants, if it is non-null.

- If, before Holocene activation, `eip1559Parameters` is non-zero, the attributes are to be considered invalid by the
  engine.
- After Holocene activation:
  - if `eip1559Params` is zero, the [canyon base fee parameter constants](../exec-engine.md#1559-parameters) are
    used.
  - if `eip1559Params` are non-null, the values from the attributes are used.

### Rationale

This type is made available in the payload attributes to allow the block builder to dynamically control the EIP-1559
parameters of the chain. As described in the [derivation - AttributesBuilder](./derivation.md#attributes-builder)
section, the derivation pipeline must populate this field from the `SystemConfig` during payload building, similar to
how it must reference the `SystemConfig` for the `gasLimit` field.

### `eip1559Params` in Header

Upon Holocene activation, the L2 block header's `nonce` field will consist of the 8-byte `eip1559Params` value.

#### Header Validity Rules

Prior to Holocene activation, the L2 block header's `nonce` field is valid iff it is equal to `u64(0)`.

After Holocene activation, The L2 block header's `nonce` field is valid iff it is non-zero.

#### Encoding

The encoding of the `eip1559Params` value is described in [`eip1559Params` encoding](#eip1559params-encoding).

#### Rationale

By chosing to put the `eip1559Params` in the `PayloadAttributes` rather than in the L1 block info transaction,
the EIP-1559 parameters for the chain are not available within history. This would place a burden on performing
historical execution, as L1 would have to be consulted for fetching the values from the `SystemConfig` contract.
Instead, we re-use an unused field in the L1 block header as to make these parameters available, retaining the
purity of the function that computes the next block's base fee from the chain configuration, parent block header,
and next block timestamp.


## Fees

New OP stack variants have different resource consumption patterns, and thus require a more flexible
pricing model. To enable more customizable fee structures, Holocene adds a new component to the fee
calculation: the `operatorFee`, which is parameterized by two scalars: the `operatorFeeScalar`
and the `operatorFeeConstant`.

### Operator Fee

The operator fee, is set as follows:

`operatorFee = (gasUsed * operatorFeeScalar / 1e6) + operatorFeeConstant`

Where:

- `gasUsed` is amount of gas used by the transaction.
- `operatorFeeScalar` is a `uint32` scalar set by the chain operator, scaled by `1e6`.
- `operatorFeeConstant` is a `uint64` scalar set by the chain operator.

#### Configuring Scalars

`operatorFeeScalar` and `operatorFeeConstant` are loaded in a similar way to the `baseFeeScalar` and
`blobBaseFeeScalar` used in the [`L1Fee`](../../protocol/exec-engine.md#ecotone-l1-cost-fee-changes-eip-4844-da).
calculation. In more detail, these scalars can be accessed in two interchangable ways.

- read from the deposited L1 attributes (`operatorFeeScalar` and `operatorFeeConstant`) of the current L2 block
- read from the L1 Block Info contract (`0x4200000000000000000000000000000000000015`)
  - using the respective solidity getter functions (`operatorFeeScalar`, `operatorFeeConstant`)
  - using direct storage-reads:
    - Operator fee scalar as big-endian `uint32` in slot `3` at offset `20`.
    - Operator fee constant as big-endian `uint64` in slot `3` at offset `16`.

### Fee Vaults

These collected fees are sent to a new vault for the `operatorFee`: the [`OperatorFeeVault`](predeploys.md#operatorfeevault).

Like the existing vaults, this is a hardcoded address, pointing at a pre-deployed proxy contract.
The proxy is backed by a vault contract deployment, based on `FeeVault`, to route vault funds to L1 securely. It is
managed by the [`OperatorFeeVaultManager`](predeploys.md#operatorfeevaultmanager).
