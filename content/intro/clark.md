+++
title = 'Covenant-less Ark'
weight = 40
+++


In the [VTXO page](vtxos), we see that the VTXO tree relies on a covenant to
make the transactions in the tree pre-determined. Ideally, we can use some kind
of introspection primitive native to Bitcoin for this, like
[CTV](https://covenants.info/proposals/ctv) or
[TXHASH](https://covenants.info/proposals/txhash). However, currently no
introspection is available in Bitcoin, so we cannot use those.

On this page we will outline a variant of Ark, called clArk, that doesn't need
an introspection primitive and hence can be implemented on Bitcoin today.


## Pre-Signed Transactions

One way to simulate such a covenant is by using pre-signed transactions. We
could call it a pseudo-covenant. If **all parties that are affected by the
covenant come together and create a big multi-sig address and then pre-sign the
desired transactions using all-of-all**, then we have a construction that works
very similar to a covenant. Namely, the covenant can only be broken if all
users come together again and sign another transaction, and any one user can
force all other users to keep the covenant.

In the Ark VTXO tree, all affected users are all the owners of the VTXOs in the
leaves. Hence, for a pseudo-covenant to be built, all of these users have to
coordinate to pre-sign the entire VTXO tree.


## The Senders Sign

The owners of the VTXO leaves are the receivers of the Ark transactions though.
Requiring them to participate in the round would require the receivers of
transactions to be online. This is obviously not a nice property of a payments
protocol. Since VTXOs are so similar to UTXOs and UTXOs can be created by just
sending a transaction to an address, we want VTXO transactions to have similar
behavoir.

Instead of requiring all the receivers to sign the VTXO tree, we could also let
all the senders sign the tree. After all, the senders are already actively
participating in the round by signing their forfeit transactionis. If all of
the senders, together with the ASP, each generate a new private key and use
that key to sign the VTXO tree and afterwards promise to delete that key, the
construction is **a solid pseudo-covenant as long as a single user of the
entire group keeps their promise to delete their key**.


## OOR and VTXO Cycling

Additionally, like we saw in the [OOR section](oor), we expect that regular Ark
payments will be made using the OOR model. This means that actual
sender-to-receiver payments will not happen through Ark rounds, but through
direct p2p OOR transactions. Afterwards, it will be the holders of OOR VTXOs
that will "cycle" their OOR VTXOs into a future round and swap it for a
"regular VTXO". In all those situations of VTXO cycling, the receivers of the
new VTXOs are the same parties as the senders in that round. Hence, we expect
that in the majority of cases, the senders and the receivers in Ark rounds will
be the same parties, making the pseudo-covenant construction outlined here
perfectly safe for these users.


