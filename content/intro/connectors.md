+++
title = 'Connectors'
weight = 20
+++


## Ark Rounds

The Ark protocol is driven by a process called *rounds*. Each round, the Ark server allows users, owners of VTXOs, to refresh their VTXOs in return for new ones. Ark refreshes as such can be thought about similarly as regular bitcoin transactions where in each refresh some input VTXOs are spent and some output VTXOs are created. However, this analogy is only conceptual, there are no actual "Ark transactions" in the same sense as there are bitcoin transactions.

Each successful round results in the creation of an **Ark round transaction**. This transaction contains an output that creates a new [covenant tree](vtxos#covenant-trees). This tree contains all the output VTXOs of all the Ark refreshes made in this round.

![an Ark tx in an Ark round](/diagrams/round.png)


## Forfeit Transactions

Users refreshing VTXOs in a round will create new VTXOs in return. However, the on-chain liquidity for these outputs is actually provided by the Ark server. In return, the users will **forfeit their input VTXOs to the Ark server**. This means that the Ark server will effectively "front" the liquidity at the time of the round and will be able to unlock this liquidity at a later point in time when the input VTXOs expire.

So, users that enter an Ark round with some input VTXOs will forfeit these VTXOs using a **forfeit transaction**, a transaction that effectively transfers ownership of the VTXO to the Ark server. In the VTXO policy section we outline the [forfeit clause](vtxos#forfeit-clause) that is a multisig between the user and the Ark server. This multisig is used for the forfeit transaction.

![forfeit tx](/diagrams/forfeit.png)


## Connectors

The forfeit transaction as described above is trivially unsafe. If the user would sign that transaction, they would be handing over control to their VTXO to the Ark server without having any guarantee that it will get back a new VTXO in the Ark round. We want to **make the forfeiting of the input VTXOs atomic with the creation of the new VTXOs** in the Ark round transaction.

To do this, the round transaction will create another covenant tree that contains one leaf for each input VTXO. These leaves are called connector outputs and will have no real value, but if the users take one of these connectors into their forfeit transaction, that means that **the forfeit transaction is only valid if the Ark round transaction confirms** on the chain.

![forfeit tx with a connector input](/diagrams/connectors.png)



