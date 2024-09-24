# L1 Block Attributes

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Overview](#overview)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Overview

The L1 block attributes transaction is updated to include the EIP-1559 parameters and the extra scalars
for the `operatorFee`.

| Input arg         | Type    | Calldata bytes | Segment |
| ----------------- | ------- | -------------- | ------- |
| {0x098999be}      |         | 0-3            | n/a     |
| baseFeeScalar     | uint32  | 4-7            | 1       |
| blobBaseFeeScalar | uint32  | 8-11           |         |
| sequenceNumber    | uint64  | 12-19          |         |
| l1BlockTimestamp  | uint64  | 20-27          |         |
| l1BlockNumber     | uint64  | 28-35          |         |
| basefee           | uint256 | 36-67          | 2       |
| blobBaseFee       | uint256 | 68-99          | 3       |
| l1BlockHash       | bytes32 | 100-131        | 4       |
| batcherHash       | bytes32 | 132-163        | 5       |
| eip1559Denominator   | uint64  | 164-171        | 6       |
| eip1559Elasticity    | uint64  | 172-179        |         |
| operatorFeeScalar  | uint32  | 180-183        |         |
| operatorFeeConstant    | uint64  | 184-191        |         |
| UNUSED: MUST BE ZERO   | uint32  | 192-195        |         |

In the first L2 block after the Isthmus activation block, the Isthmus L1 attributes are first used.

The pre-Isthmus values are migrated over 1:1.
Blocks after the Isthmus activation block contain all pre-Isthmus values 1:1,
and also set the following new attributes:

- The `operatorFeeScalar` is set to `0`.
- The `operatorFeeConstant` is set to `0`.
