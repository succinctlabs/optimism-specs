# L2 Execution Engine

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [`L2ToL1MessagePasser` Storage Root in Header](#l2tol1messagepasser-storage-root-in-header)
  - [Timestamp Activation](#timestamp-activation)
  - [Header Validity Rules](#header-validity-rules)
  - [Header Withdrawals Root](#header-withdrawals-root)
    - [Rationale](#rationale)
    - [Forwards Compatibility Considerations](#forwards-compatibility-considerations)
    - [Client Implementation Considerations](#client-implementation-considerations)
- [Fees](#fees)
  - [Configurable fees](#configurable-fees)
    - [Configuring scalars](#configuring-scalars)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Fees

New OP stack variants have different resource consumption patterns, and thus require a more flexible
pricing model. To enable more customizable fee structures, Holocene adds a new component to the fee
calculation: the `ConfigurableFee`, which is parameterized by two scalars: the `configurableFeeScalar`
and the `configurableFeeConstant`.

### Configurable fees

The configurable fee is set as follows:

`configurableFee = gas_used * configurableFeeScalar + configurableFeeConstant`

Where:

- `gas_used` is amount of gas used by the transaction.
- `configurableFeeScalar` is a `uint64` scalar set by the chain operator.
- `configurableFeeConstant` is a `uint64` scalar set by the chain operator.

These collected fees are sent to the `SequencerFeeVault`.

#### Configuring scalars

`configurableFeeScalar` and `configurableFeeConstant` are loaded in a similar way to the `baseFeeScalar` and
`blobBaseFeeScalar` used in the [`L1Fee`](../../protocol/exec-engine.md#ecotone-l1-cost-fee-changes-eip-4844-da).
calculation. In more detail, these scalars can be accessed in two interchangable ways.

- read from the deposited L1 attributes (`configurableFeeScalar` and `configurableFeeConstant`) of the current L2 block
- read from the L1 Block Info contract (`0x4200000000000000000000000000000000000015`)
  - using the respective solidity getter functions (`configurableFeeScalar`, `configurableFeeConstant`)
  - using direct storage-reads:
    - Configurable fee scalar as big-endian `uint64` in slot `3` at offset `24`.
    - Configurable fee constant as big-endian `uint64` in slot `3` at offset `16`.
