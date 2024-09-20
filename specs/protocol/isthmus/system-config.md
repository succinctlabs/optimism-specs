# System Config

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [System config contents (version 0)](#system-config-contents-version-0)
  - [Scalars](#scalars)
    - [Isthmus `scalar`, `overhead` (`uint256,uint256`) change](#isthmus-scalar-overhead-uint256uint256-change)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## System config contents (version 0)

### Scalars

In order to allow rollup operators to adjust the new `ConfigurableFeeScalar` and `ConfigurableFeeConstant`, a change to
the `scalar` and `overhead` encoding is necessary.

#### Isthmus `scalar`, `overhead` (`uint256,uint256`) change

After Isthmus activation:

- A new version to the `scalar` encoding is added. It is described as follows.

\*Byte ranges are indicated with `[` (inclusive) and `)` (exclusive).

- `0`: scalar-version byte
- `[1, 32)`: depending scalar-version:
  - Scalar-version `2`:
    - `[1, 8)`: padding, must be zero.
    - `[8, 16)`: big-endian `uint64`, encoding the `configurableFeeScalar`
    - `[16, 24)`: big-endian `uint64`, encoding the `configurableFeeConstant`
    - `[24, 28)`: big-endian `uint32`, encoding the `blobBaseFeeScalar`
    - `[28, 32)`: big-endian `uint32`, encoding the `baseFeeScalar`
    - This version adds the `configurableFeeScalar` and the `configurableFeeConstant`.

The `configurableFeeScalar` and `configurableFeeConstant` are incorporated into the L2 through the
[Isthmus L1 attributes deposit transaction calldata](l1-attributes.md).
