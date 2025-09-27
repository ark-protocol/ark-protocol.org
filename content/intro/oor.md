+++
title = 'Out-of-Round Payments'
weight = 30
+++


Ark rounds are infrequent and they require liquidity to be provided by the Ark server, which will mean the users will be charged a fee for locking up this liquidity. Therefore, rounds are not suitable for Ark payments, because while still a lot cheaper than on-chain transactions, they would result in slow payments with excess costs.

We can do better with **out-of-round (OOR) payments**, sometimes referred to as *arkoor*.


## OOR transaction

To make an OOR payment, we will re-use the [forfeit clause](vtxos#forfeit-clause) in the VTXO policy. Instead of entering a round, a user can create a transaction, looking very similar to a forfeit transaction, that sends the VTXO to a new VTXO output of another user. The sender then **requests the Ark server to co-sign that transaction** and passes on the transaction to the receiving user.

![OOR transaction](/diagrams/oor.png)

At that point, the receiving user has an actual VTXO as well. It looks a bit different than a VTXO created in an Ark round, it is one transaction longer, but it has the same properties—a user that received an OOR VTXO can use that VTXO to do all the things one could do with a regular VTXO: use it as an input in an Ark round, or send a further OOR payment.

![OOR tx from an OOR vtxo](/diagrams/ooroor.png)

![OOR vtxo used in round](/diagrams/oor-round.png)


## Trust Model

While OOR payments are very convenient—they're instant and don't require liquidity—they do change the trust model. The chain of off-chain transactions now no longer only contains covenant links, it also includes a spend secured by a multisig between the Ark server and the sending party. So as a receiver of an OOR transaction, **the user is relying on these two parties to not collude and double spend** the transaction. If there are multiple off-chain payments in the chain, then the receiver must rely on any one of the sending parties in the chain not colluding with the Ark server to double spend.

In a properly functioning Ark, the Ark server commits to preventing such double spends, and has a strong economic incentive to maintain that commitment. In the case of a double spend, the signatures for both spends become known to the affected user and this user can cryptographically prove to the public that the Ark server has violated this protocol guarantee. Since the Ark server has a financial incentive to maintain its reputation and user base, they have a strong incentive to uphold this guarantee.

Additionally, the OOR model is only ever temporary and users have control over how long they are exposed to it. If a user prefers the in-round model, after receiving an OOR transaction they can immediately submit the OOR VTXO to the next Ark round and refresh it for a regular VTXO that is fully trustless. Or they can wait till close to expiry to refresh, saving on fees. But they must always eventually refresh.

OOR payments make Ark payments faster than relying on in-round payments, and provide users control over their preferred security model.
