+++
archetype = "home"
title = "Ark Protocol"
+++



Ark is a layer-two protocol for making off-chain Bitcoin transactions.
Initially published on the bitcoin-dev mailing list as *TBDXXX* by Burak, is
has since been named Ark and the protocol design has advanced significantly.

The goal and result of the Ark protocol is a payments system where people can
make Bitcoin transactions at very low cost and without requiring any setup. The
Ark model very closely resembles the UTXO model, which is a key differentiator
with Lightning.



#### Lightning Network

Intuitively, we are inclined to compare Ark with the Lightning Network, because
it is currently the only second layer on the Bitcoin network.

However, the Ark protocol design has very little in common with Lightning's
design. Lightning is a payment channel network, where a network of participants
manage channels amongs pairs of them and payments are routed over paths along
these payment channels.

The main difficulty that users experience when trying to use Lightning is that
users need to have at least one payment channel in order to be able to receive
payments. On top of having a channel, they also need to have some **inbound
liquidity**, meaning that their channel counterparty has to commit some amount
of money into the channel that will then be available for the new user to
receive.

This onboarding process of creating one or more channels and acquiring inbound
liquidity is the main drawback of the Lightning protocol that the Ark protocol
doesn't have.


## Shared UTXOs

The Ark protocol is built around the idea of **shared UTXOs**. In principle,
Lightning also has shared UTXOs, however they are only shared between the two
channel counterparties. In Ark, UTXOs are shared among a large amount of users,
potentially up to tens or hundreds of thousands of users.

While in the Lightning Network, payments are made by atomically shifting an
amount of Bitcoin in a series of channels, in Ark, payments are made by
exchanging a share in an existing shared UTXO for a share in a new shared UTXO.
These shares of UTXOs will be called **virtual UTXOs, or VTXOs**.

These exchanges are coordinated by a central party, an **Ark Service Provider
(ASP)**, that facilitates payments, but is never a custodian. Because the UTXO
is shared among the users in the shared UTXO, they can claim back there Bitcoin
at all times without depending on the ASP by broadcasting their VTXO
transactions.


## Rounds and Connectors

Traditional Ark payments happen in so-called **rounds**. The ASP will organize
these rounds periodically to give the users in the Ark the chance to make
payments. During a round, any user that has a VTXO in the Ark can participate
and request the service provider to include a transaction for them in the
round. The ASP will then construct a new shared UTXO, which will include all
the payments that are made inside this round.

The participating users will then relinquish their existing VTXO to the ASP by
signing an off-chain **forfeit transaction**, which sends their input VTXO to
the ASP. In return, the ASP creates a new shared on-chain UTXO with the desired
output VTXOs and broadcast that transaction.

The way these transaction are made atomic, meaning that the sending doesn't
forfeit their VTXOs without having a guarantee that the newly created VTXOs
will actually confirm, is by making the forfeit transaction dependent on the
newly created shared UTXO transaction. This is done using **connector outputs**
which are outputs that are part of the shared UTXO transaction and that will be
used as an input on the forfeit transactions.


## Arkoor

These rounds require interaction between participating users and because they
result in an on-chain transaction that needs to be confirmed before the
transactions can be considered final. Because of this, it takes time to hold rounds.

In order for users to be able to spend their VTXOs more rapidly without needing
to participate in Ark rounds, there is a way to make **out-of-round (OOR)
transactions**. These are payments that participants in an Ark can make
instantly, directly from one party to another.

OOR payments are cosigned by the ASP. In theory, the trust model for OOR
payments relies on the sender and the ASP not colluding to make a double spend.
The receiver can then decide to either keep the OOR VTXO and rely on the ASP
not wanting to lose its reputation by being proven to be dishonest, or decide
to not have this trust and participate in the next Ark round to cycle this OOR
VTXO into a new regular VTXO.




