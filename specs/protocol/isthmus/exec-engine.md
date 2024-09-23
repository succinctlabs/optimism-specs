# L2 Execution Engine

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Fees](#fees)
  - [Operator fees](#operator-fees)
    - [Configuring scalars](#configuring-scalars)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Fees

New OP stack variants have different resource consumption patterns, and thus require a more flexible
pricing model. To enable more customizable fee structures, Isthmus adds a new component to the fee
calculation: the `operatorFee`, which is parameterized by two scalars: the `operatorFeeScalar`
and the `operatorFeeConstant`.

### Operator fees

The operator fee, in wei, is set as follows:

`operatorFee = (gasUsed * operatorFeeScalar / 1e6) + operatorFeeConstant`

Where:

- `gasUsed` is amount of gas used by the transaction.
- `operatorFeeScalar` is a `uint32` scalar set by the chain operator, scaled by `1e6`.
- `operatorFeeConstant` is a `uint64` scalar set by the chain operator.

These collected fees are sent to the `SequencerFeeVault`.

#### Configuring scalars

`operatorFeeScalar` and `operatorFeeConstant` are loaded in a similar way to the `baseFeeScalar` and
`blobBaseFeeScalar` used in the [`L1Fee`](../../protocol/exec-engine.md#ecotone-l1-cost-fee-changes-eip-4844-da).
calculation. In more detail, these scalars can be accessed in two interchangable ways.

- read from the deposited L1 attributes (`operatorFeeScalar` and `operatorFeeConstant`) of the current L2 block
- read from the L1 Block Info contract (`0x4200000000000000000000000000000000000015`)
  - using the respective solidity getter functions (`operatorFeeScalar`, `operatorFeeConstant`)
  - using direct storage-reads:
    - Operator fee scalar as big-endian `uint32` in slot `3` at offset `20`.
    - Operator fee constant as big-endian `uint64` in slot `3` at offset `16`.
