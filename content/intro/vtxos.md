+++
title = 'VTXOs'
weight = 10
+++


A **virtual transaction output (VTXO)** is a bitcoin transaction output held by a user but not broadcast or confirmed on-chain. Just as bitcoin UTXOs are destroyed and created in every transaction, VTXOs are spent and created in Ark transactions.

The main difference between a VTXO and a UTXO is that VTXOs are off-chain. A VTXO represents valid bitcoin transactions that could be broadcast and confirmed, but the user chooses not to broadcast them yet. In effect, a VTXO is **the ability to create a UTXO** at a later time.

Since creating UTXOs on the bitcoin blockchain costs fees, the purpose of Ark is to enable users to transact using VTXOs without paying transaction fees to convert them into UTXOs.


## Covenants

The construction of Ark VTXOs requires pre-determining a group of transactions ahead of time. There are two approaches: pre-signing transactions and keeping them off-chain, or using **covenants**—a proposed bitcoin protocol primitive that allows users to pre-determine a series of transactions ahead of time without pre-signing them.

For Ark, VTXOs are easiest to understand when built with a simple covenant like [`OP_CHECKTEMPLATEVERIFY`](https://covenants.info/proposals/ctv/). However, until covenants are available on bitcoin mainnet, VTXOs can use **pseudo covenants** that emulate covenants through a series of pre-signed transactions. To create a pseudo covenant, multiple signers pre-sign a series of transactions then delete their keys so that no other alternative series of transactions can be made to replace the pre-signed ones.

Below is an example of a simple covenant. The transaction above the line is confirmed on-chain and it has a *covenant output*. The transaction below the line is off-chain—Alice, Bob, Carol and Dave can broadcast it anytime to claim their 1 BTC outputs. These outputs are VTXOs: unconfirmed transaction outputs that users can create on-chain any time by broadcasting a transaction.

![simple covenant example](/diagrams/simple-covenant.png)


## Covenant Trees

In the simple example above, any user that wants to access their UTXO must broadcast a transaction that puts all users' outputs on-chain. For Ark, we want individual users to go on-chain independently without affecting others' VTXOs, while ensuring the design is optimized to minimize single-user exit costs. Instead of one transaction with multiple outputs, we build a tree of transactions where each user owns a leaf output.

![covenant tree](/diagrams/covenant-tree.png)

This tree construction increases the total on-chain footprint if all users exit, but dramatically reduces the individual footprint per user compared to using a single transaction, especially as the tree grows larger. Since users rarely need to exit under normal circumstances—exit is just a fail-safe—we optimize for single-user exits.

To conclude, what we call a **VTXO** is a set of transactions a user possesses, the validity of which cannot be revoked by anyone, that allow this user to create a certain UTXO on-chain as and when required.

![vtxo graphic](/diagrams/vtxo.png)


## Ark VTXO Policy

The VTXOs that are created in the Ark protocol are not exactly as described above. In order for the Ark protocol to work, we need to make two changes to the covenant tree construction.


### Forfeit Clause

The tree leaves above contain scripts with simple user public keys, allowing users to spend them anytime. We're going to modify this script while preserving the user's ability to claim the output:

```
(A + S) OR (A after 24h)
```

Where `A` is the user's pubkey and `S` is the Ark server's pubkey.

With this change, the user still has a guarantee that he can access the full amount, but he would have to wait for 24 hours from the moment the output was confirmed. Meanwhile, the user and the Ark server together can create transactions that spend this leaf immediately without having to wait for the timeout. We'll later use the (A + S) clause in our [*forfeit transactions*](/intro/connectors#forfeit-transactions) and [*out-of-round payments*](/intro/oor).


### VTXO Expiry

The second change modifies intermediate node policies. In the examples above, these were simple covenants—outputs spendable only by a single pre-determined transaction. We'll keep this but add an extra clause that activates after a certain time period:

```
cov OR (S after 14d)
```

This means that after 14 days, the Ark server can reclaim all the money in the entire tree. We will see that this change puts an effective expiration on each VTXO—a deadline where both the user *and* the Ark server can each unilaterally spend the bitcoin. Users will have to periodically spend their VTXOs and send them back to themselves (known as a *refresh* or *batch*) to reset the expiry time.

![covenant tree with modified policies](/diagrams/covenant-tree-policies.png)
