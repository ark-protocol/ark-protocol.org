+++
title = 'Connectors'
weight = 20
+++


## Ark Rounds

The Ark protocol is driven by the ASP by a process called *rounds*. Each round,
the ASP allows users, owners of VTXOs, to spend some of their VTXOs in return
for new ones. Ark payments as such can be thought about similarly as
regular Bitcoin transactions where in each transaction some input UTXOs are
spent and some output UTXOs are created. Ark payments function similarly,
but with VTXOs. However, this analogy is only conceptual, there are no actual
"Ark transactions" in the same sense as there are Bitcoin transactions.

Each succesful round results in an **Ark round transaction** to be created.
This transaction contains an output that creates a new [covenant
tree](vtxos#covenant-trees). This tree contains all the output VTXOs of
all the Ark payments made in this round.

![an Ark tx in an Ark round](/diagrams/round.png)


## Forfeit Transactions

Users spend VTXOs in a round and will create new VTXOs in return. However, the
on-chain liquidity for these outputs is actually provided by the ASP. In
return, the users will **forfeit their input VTXOs to the ASP**. This means
that the ASP will effectively "front" the liquidity at the time of the round
and will be able to unlock this liquidity at a later point in time when the
input VTXOs expire.

So, users that enter an Ark round with some input VTXOs will forfeit these
VTXOs using a **forfeit transaction**, a transaction that effectively transfers
ownership of the VTXO to the ASP. In the VTXO policy section we outline the
[forfeit clause](vtxos#forfeit-clause) that is a multi-sig between the
user and the ASP. This multi-sig is used for the forfeit transaction.

![forfeit tx](/diagrams/forfeit.png)


## Connectors

The forfeit transaction above is trivially unsafe. If the user would sign that
transaction, they would be handing over control to their VTXO to the ASP
without having any guarantee that it will get back a new VTXO in the Ark round.
We want to **make the forfeiting of the input VTXOs atomic with the creation of
the new VTXOs** in the Ark round transaction.

To do this, the round transaction will create another covenant tree that
contains one leaf for each input VTXO. These leaves are called connector
outputs will have no real value, but if the users take one of these connectors
into their forfeit transaction, that means that **the forfeit transaction is
only valid if the Ark round transaction confirms** on the chain.

![forfeit tx with a connector input](/diagrams/connectors.png)



