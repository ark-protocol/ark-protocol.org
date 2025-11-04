+++
archetype = "home"
title = "Ark Protocol"
+++



Ark is a layer 2 protocol for making off-chain bitcoin transactions. Originally published as *TBDXXX* by Burak on the bitcoin-dev mailing list, it has since been named Ark and the protocol design has advanced significantly.

The goal of Ark is to enable bitcoin transactions at very low cost without requiring extensive setup (like opening and funding channels). The Ark model closely resembles bitcoin's UTXO model.

There are currently two implementations of the Ark protocol in development:

| Implementation | Website | Documentation | Repository |
|---|---|---|---|
| **Second** | [second.tech](https://second.tech) | [docs.second.tech](https://docs.second.tech) | [gitlab.com/ark-bitcoin](https://gitlab.com/ark-bitcoin) |
| **Arkade** | [arkadeos.com](https://arkadeos.com) | [docs.arkadeos.com](https://docs.arkadeos.com) | [github.com/arkade-os](https://github.com/arkade-os) |

#### Comparing Ark with the Lightning Network

Intuitively, we are inclined to compare Ark with the Lightning Network, because Lightning is currently the only true layer 2 on the bitcoin network.

However, the Ark protocol design has very little in common with Lightning's design. Lightning is a payment channel network where participants create channels between pairs of users, forming a network through which payments are routed along paths between these channels. Ark, on the other hand, is a client-server protocol, with users coordinating their payments via a central server while retaining control over their own bitcoin. 

The main difficulty users experience with Lightning is the cost and complexity of onboarding. Users must open at least one payment channel to receive payments, requiring an on-chain transaction. Additionally, new users need to acquire **inbound liquidity**—their channel counterparty must commit bitcoin into the channel to make it available for receiving.

Ark eliminates this onboarding complexity entirely. There are no channels to open or liquidity for the user to manage.


## Shared UTXOs

The Ark protocol is built around **shared UTXOs**. While Lightning also uses shared UTXOs, they're only shared between two channel counterparties. In Ark, UTXOs are shared among a large number of users—potentially hundreds of thousands!

In Lightning, payments atomically shift bitcoin amounts across a series of channels. In Ark, payments are made by exchanging a share in an existing shared UTXO for a share in a new shared UTXO. These shares are called **virtual UTXOs, or VTXOs**. Shares in an on-chain UTXO are achieved by creating a tree of off-chain transactions, with each user holding their associated branch and leaf transactions. 

VTXO creation and transfers are coordinated by users via a central party, an **Ark server**, without ever giving the Ark server custody over the bitcoin. Users can always claim their bitcoin on-chain without depending on the Ark server by broadcasting their VTXO transactions in consecutive order until the bitcoin is released to an output only the users control.


## Rounds and Connectors

Ark's transaction trees are created in **rounds**. The Ark server organizes these rounds periodically, giving users the opportunity to forfeit old VTXOs for new ones—known as a *refresh* or *batch*. During a round, any user with a VTXO can participate and request the Ark server to include a refresh for them. The Ark server then constructs a new shared UTXO containing all refreshes from that round.

Participating users sign an off-chain **forfeit transaction** that sends their input VTXO to the Ark server. In return, the Ark server creates and broadcasts a new shared on-chain UTXO with the desired output VTXOs.

The round's refresh operations are made atomic through **connector outputs**—outputs in the shared UTXO transaction that are used as inputs for the forfeit transactions. This ensures users don't forfeit their VTXOs without a guarantee that the new VTXOs will confirm.


## Payments and Arkoor

Rounds require user interaction and result in on-chain transactions that need confirmation. Because of this, it takes time to hold and complete rounds. 

Therefore, payments on Ark are handled out-of-round (*OOR* or *arkoor*). This allows Ark users to make instant payments between each other without waiting for rounds.

OOR payments are cosigned by the Ark server. The trust model relies on the sender and Ark server not colluding to double-spend. Receivers can either accept this trust assumption and keep the OOR VTXO until expiry, or participate in the next round to refresh it into an in-round VTXO.
