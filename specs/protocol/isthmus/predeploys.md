# Overview

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Predeploys](#predeploys)
  - [L1Block](#l1block)
    - [Interface](#interface)
      - [`setL1BlockValuesIsthmus`](#setl1blockvaluesisthmus)
      - [`setIsthmus`](#setisthmus)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Since we now need to include the configurable fee scalars in the L1 Attri

## Predeploys

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
