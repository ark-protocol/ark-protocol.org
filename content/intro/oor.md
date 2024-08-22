+++
title = 'Out-of-Round Payments'
weight = 30
+++



Ark rounds are infrequent and they require liquidity to be provided by the ASP,
which will mean the users will be charged a fee for locking up this liquidity.
Therefore, regular Ark payments, while still a lot cheaper than on-chain
transactions, are still slow and might have a non-trivial cost.

This page introduces an optional alternative payment method in Ark:
out-of-round (OOR) payments.


## OOR transaction

To make an OOR payment, we will re-use the [forfeit
clause](vtxos#forfeit-clause) in the VTXO policy. Instead of entering a round,
a user can create a transaction, looking very similar to a forfeit transaction,
that sends the VTXO to a new VTXO output of another user. It then **asks the
ASP to co-sign that transaction** and can then pass on the transaction to the
receiving user.

![OOR transaction](/diagrams/oor.png)

At that point, the receiving user has an actual VTXO as well. It looks a bit
different than a regular VTXO created in an Ark round, it is one transaction
longer, but it has the same properties. And a user that received an OOR VTXO
can use that VTXO to do all the things one could do with a regular VTXO:
use it as an input in an Ark round, or send an OOR transaction.

![OOR tx from an OOR vtxo](/diagrams/ooroor.png)

![OOR vtxo used in round](/diagrams/oor-round.png)


## Trust Model

While OOR payments are very convenient, they are instant and don't require
liquidity, they do change the trust model a little bit. The chain of off-chain
transactions now no longer only contains covenant links, it also includes a
regular multi-sig spends between the ASP and each of the sending parties. So as
a receiver of an OOR transaction, **you're relying on these two parties to not
collude and double spend** the transaction.

In a reasonably-sized Ark, the ASP will promise to never make such a double
spend, and has a strong incentive to keep that promise. In the case of a double
spend, the signatures for both spends become known to the cheated user and this
user can trivially proof to the public that the ASP has broken this promise.
Since the ASP has a financial incentive to keep their business, they have a
strong incentive not to break this promise.

Additionally, the OOR model is optional, a user receiving an OOR transaction
can immediatelly submit the OOR VTXO to the next Ark round and swap it for a
regular VTXO that has the fully trustless property. As such, OOR payments make
for faster Ark payments and optionally allow users to avoid paying the
liquidity fee.
