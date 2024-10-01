# System Config

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [System config contents (version 0)](#system-config-contents-version-0)
  - [Scalars](#scalars)
    - [Holocene `scalar`, `overhead` (`uint256,uint256`) change](#holocene-scalar-overhead-uint256uint256-change)
    - [Interface](#interface)
      - [`operatorFeeScalar`](#operatorfeescalar)
      - [`operatorFeeConstant`](#operatorfeeconstant)
      - [`setOperatorFeeScalars`](#setoperatorfeescalars)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## System config contents (version 0)

### Scalars

In order to allow rollup operators to adjust the new `operatorFeeScalar` and `operatorFeeConstant`, a change to
the `scalar` and `overhead` encoding is necessary. As before, updates to the `operatorFeeScalar` and
`operatorFeeConstant` are signaled to the L2 through the `ConfigUpdate` log-event, specifically
using the `UpdateType.FEE_SCALARS` enum.

#### Holocene `scalar`, `overhead` (`uint256,uint256`) change

After Holocene activation:

- A new version to the `scalar` encoding is added. It is described as follows.

\*Byte ranges are indicated with `[` (inclusive) and `)` (exclusive).

- `0`: scalar-version byte
- `[1, 32)`: depending scalar-version:
  - Scalar-version `2`:
    - `[1, 12)`: padding, must be zero.
    - `[12, 16)`: big-endian `uint32`, encoding the `operatorFeeScalar`
    - `[16, 24)`: big-endian `uint64`, encoding the `operatorFeeConstant`
    - `[24, 28)`: big-endian `uint32`, encoding the `blobBaseFeeScalar`
    - `[28, 32)`: big-endian `uint32`, encoding the `baseFeeScalar`
    - This version adds the `operatorFeeScalar` and the `operatorFeeConstant`.

The `operatorFeeScalar` and `operatorFeeConstant` are incorporated into the L2 through the
[Holocene L1 attributes deposit transaction calldata](l1-attributes.md).

#### Interface

The following functions are updated to access and modify the `operatorFeeScalar` and `operatorFeeConstant`:

##### `operatorFeeScalar`

```solidity
function operatorFeeScalar()(uint32)
```

##### `operatorFeeConstant`

```solidity
function operatorFeeConstant()(uint64)
```

##### `setOperatorFeeScalars`

```solidity
function setOperatorFeeScalar(uint32 _operatorFeeScalar, uint64 _operatorFeeConstant)()
```
