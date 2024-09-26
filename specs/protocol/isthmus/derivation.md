# Isthmus L2 Chain Derivation Changes

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Network upgrade automation transactions](#network-upgrade-automation-transactions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Network upgrade automation transactions

Upgrades to these contracts requires some special treatment for these guys.

The Isthmus hardfork activation block contains the following transactions, in this order:

- L1 Attributes Transaction
- User deposits from L1
- Network Upgrade Transactions
  - L1Block deployment
  - Update L1Block Proxy ERC-1967 Implementation
  - L1Block Enable Isthmus
  - GasPriceOracle deployment
  - Update GasPriceOracle Proxy ERC-1967 Implementation
