# Overview

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Predeploys](#predeploys)
  - [GasPriceOracle](#gaspriceoracle)
    - [Interface](#interface)
      - [`getOperatorFee`](#getoperatorfee)
  - [L1Block](#l1block)
    - [Interface](#interface-1)
      - [`setL1BlockValuesIsthmus`](#setl1blockvaluesisthmus)
      - [`setIsthmus`](#setisthmus)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Since we now need to include the operator fee scalars in the L1 Attributes, we need new methods to
`L1Block` in order to set the new scalars.

## Predeploys

### GasPriceOracle

In order to maintain accurate offchain fee estimation, the `GasPriceOracle` must be updated to allow users
to estimate the operator fee.

#### Interface

##### `getOperatorFee`

This function calculates the operator fee based on the expected amount of gas used for a certain transaction.

```function
function getOperatorFee(uint256 gasUsed)(uint256)
```

### L1Block

#### Interface

##### `setL1BlockValuesIsthmus`

This function MUST only be callable by the `DEPOSITOR_ACCOUNT`. It is a replacement
for `setL1BlockValuesHolocene` and its calldata is defined in [L1 Attributes](./l1-attributes.md).

```function
function setL1BlockValuesIsthmus()
```

##### `setIsthmus`

This function is meant to be called once on the activation block of the isthmus network upgrade.
It MUST only be callable by the `DEPOSITOR_ACCOUNT` once. When it is called, it MUST call
call each getter for the network specific config and set the returndata into storage.
