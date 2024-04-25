+++
title = 'VTXOs'
weight = 10
+++


A **virtual transaction output (VTXO)** is a Bitcoin transaction output that is
held by a user, but is not confirmed on the chain. The Bitcoin protocol is made
out of UTXOs (Unspent Transaction Outputs), which are destroyed and created in
every transaction. Similarly, in the Ark protocol, VTXOs will be spend and
created in Ark transactions.

The main difference between a VTXO and a UTXO is that VTXOs are off-chain. This
means that there exist a series of valid Bitcoin transactions, that could be
broadcasted to the network and confirmed on the blockchain, but that for some
reason no one has done so (yet). In effect, a VTXO is **the ability to create a
UTXO** at a later time.

Because creating UTXOs on the Bitcoin blockchain costs fees, the purpose of Ark
is for users to be able to transact using their VTXOs without ever having to
pay the transaction fees to make their VTXOs in UTXOs.


## Covenants

The construction of the Ark VTXOs relies on a way to pre-determine a group of
transactions ahead-of-time. There are two ways one can go about that, one is to
pre-sign these transactions and keep the transactions along with the signatures
off-chain. The other is to use what is called a **covenant** construction,
which is a Bitcoin protocol primitive that allows users to pre-determine a
series of transactions ahead of time without needing to pre-sign them.

For the model we use in Ark, it is easiest to think about them being built
using a simple covenant, for example
[`OP_CHECKTEMPLATEVERIFY`](https://covenants.info/proposals/ctv/), but it is
important to note that the same constructions can work with a
**pseudo-covenant**, meaning by using pre-signed transactions. To create a
pseudo-covenant, one would gather a large group of signers that pre-sign a
series of transactions and then delete the keys they used so that no other
alternative series of transactions can be made to replace the pre-signed ones.

Below you can see an example of a simple covenant. The transaction above the
line is confirmed on the chain and it has a *covenant output*. The transaction
below the line is an off-chain transaction that Alice, Bob, Carol and Dave can
use at any time they want to claim their 1 BTC output. These outputs are what
we call VTXOs. They are transaction outputs that are not confirmed on the chain
yet, but that a user can create at any time by broadcasting a transaction.

![simple covenant example](/diagrams/simple-covenant.png)


## Covenant Trees

In the above simple example, if any one user wants access to their UTXO they
would have to broadcast a transaction that puts all outputs on the chain. For
Ark, we want to optimize such that the cost for a single user to go on-chain is
minimized. We can do this by instead of using a single transaction, build a
tree of subsequent transactions, where each user owns one leaf outputs.

![covenant tree](/diagrams/covenant-tree.png)

In a tree-like construction as such, the total footprint for all users to go
on-chain is obviously increased, but the individual footprint per user will be
a lot smaller compared to a single transaction, when the number of users
participating in the tree grows. And on Ark, we will see that under normal
circumstances, users never have to exit, the possibility to exit is just a
fail-safe. This is why we optimize for the single-user exit case.

To conclude, what we call a **VTXO** is a set of transactions a user possesses,
the validity of which cannot be revoked by anyone, that allow this user to
create a certain UTXO on the blockchain.

![vtxo graphic](/diagrams/vtxo.png)


## Ark VTXO Policy

The VTXOs that are created in the Ark protocol are not exactly as described
above. In order for the Ark protocol to work, we need to make two changes to
the covenant tree construction.


### Forfeit Clause

The leaves in the trees above look like they contain a script with a simple
public key from the user. This would mean that the user can spend the leaf at
any time. We're going to make a small change to this script, without taking
away the ability of the user to claim the output. The policy we use is as
follows:

```
(A + S) OR (A after 24h)
```

Where `A` is the user's pubkey and `S` is the ASP's pubkey.

With this change, the user still has a guarantee that it can access the full
amount, however he would have to wait for 24 hours. Meanwhile, the user and the
ASP together can create transactions that can spend this leaf immediatelly
without having to wait for the timeout. We will later see that we will use this
first clause in our [*forfeit
transactions*](/intro/connectors#forfeit-transactions) or for [*out-of-round
payments*](/intro/oor).

### VTXO Expiry

The second change is an addition to the policy of the intermediate nodes. In
the examples above, they were simple covenants: outputs that can only ever be
spent by a single pre-determined transaction. We will keep this possibility,
but we will add an extra clause that activates after a certain time period.

```
cov OR (S after 14d)
```

This means that after 14 days, the ASP can re-claim all the money in the entire
tree. We will see that this change puts an effective expiration on each VTXO.
Users will have to periodically spend their VTXOs and send them back to
themselves, so as to reset the expiry time.

![covenant tree with modified policies](/diagrams/covenant-tree-policies.png)
