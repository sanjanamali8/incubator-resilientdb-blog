---
layout: article
title: UTXO On Nexres
author: Junchao Chen
tags: NexRes
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/nexres/coin.jpg
excerpt: UTXO Implemention using Nexres.
---

An unspent transaction output ([UTXO](https://en.wikipedia.org/wiki/Unspent_transaction_output)) model helps us to make crypto transactions more efficient and it is a crucial core part in Bitcoin.
In this document, we introduce the UTXO implementation on Nexres.

## Crypto key
Crypt key is a private-public key pair to provide the security of the transaction. 
We use ECDSA as the encoding algorithm and sign each message.

## Wallet
Wallet manages the coins of each user. The address of each user is created by its public key,
using [bech32](https://github.com/fiatjaf/bech32) as encoding as what Bitcoin does, also wraped by ripemd160 and sha256.
 > address = bech32Encode(ripemd160(sha256(public key)))

## UTXO
UTXO is a data structure representing a transaction of a wallet user, including some inputs and outputs. 
Each input is a transaction that transfers coins from other users. 
In the UTXO on Nexres, the input contains the input transaction ids and the output index of the output transaction that deliver coins to the user.
All these input transactions is the available input of the current transaction,
and the outputs indicate how to deliver these coins from these transactions. It contains the addresses of the delivered users, how much coins to be delivered, and their public keys.
The sum of the inputs should be larger than the sum of the outputs. The delta can be used as the gas fee.

<img src="/assets/images/nexres/utxo.jpg"  style="zoom: 60%;" />

## Security
Each transaction will be signed by the address of the owner and the signatures can be verified by the public key inside its input transactions. Plus, the addresses of the input transactions should be the same as the address of the issue transaction.
Therefore, it should guarantee that each transaction can only be spent once.

## System Archiecture
![utxo_nexres](/assets/images/nexres/utxo_nexres.jpg)
UTXO is running on Nexres and managed by its local UTXO Manager. Each transaction will go through the consensus protocol 
by Nexres and is saved locally.

UTXO Manager iteratively fetches the transactions and executes the UTXO data in each transaction.
All the valid UTXO data will be calculated as coins and saved into the user wallets, so that they can view their accounts.
What's more, the transactions will be handled by a transaction pool so that not only the UTXO manager can keep checking 
whether the transacion has been spent or users can be able to trace the transaction history.
