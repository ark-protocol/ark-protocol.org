+++
title = 'Covenant-less Ark'
weight = 40
+++


On the [VTXO page](vtxos), we see that the VTXO tree relies on a covenant to make the transactions in the tree pre-determined. Ideally, we can use some kind of introspection primitive native to bitcoin for this, like [CTV](https://covenants.info/proposals/ctv) or [TXHASH](https://covenants.info/proposals/txhash). However, currently no introspection is available in bitcoin.

This page outlines a variant of Ark, called clArk, that doesn't require an introspection primitive and can therefore be implemented on bitcoin today.


## Pre-Signed Transactions

One way to simulate such a covenant is by using pre-signed transactions, which can be referred to as a pseudo-covenant. If **all parties that are affected by the covenant come together and create a multisig address and then pre-sign the desired transactions using an all-of-all signature scheme**, then we have a construction that works very similarly to a covenant. The covenant can only be broken if all users come together again and sign another transaction, and any one user can force all other users to maintain the covenant.

In the Ark VTXO tree, all affected users are all the owners of the VTXOs in the leaves. Hence, for a pseudo-covenant to be built, all of these users have to coordinate to pre-sign the entire VTXO tree.


## VTXO Refresh and Key Deletion Protocol

Users must periodically refresh their VTXOs to avoid them expiring. This creates a liveness requirement, because users must ensure they are regularly online to ensure they catch at least one round before their VTXOs expire.

During the round, all participating users sign their forfeit transactions. All of the refreshing users, together with the Ark server, each generate a new private key and use that key to sign the VTXO tree. Then they each delete their key. This produces a construction that is **a secure pseudo-covenant as long as at least one user in the entire group commits to deleting their key**.


## OOR and VTXO Cycling

As outlined in the [OOR section](oor), actual sender-to-receiver payments do not happen through Ark rounds, but through instant P2P OOR transactions. Subsequently, holders of OOR VTXOs will refresh their OOR VTXOs into a future round and exchange them for regular VTXOs. In VTXO refreshes, the "receiver" and "sender" of the transaction are the same party. Therefore, the refresher-signed pseudo-covenant construction outlined here is perfectly secure (as opposed to sender-signed in-round payments, as proposed in earlier iterations of the Ark protocol).


