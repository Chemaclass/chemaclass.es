+++
title = "Programmable Money"
description = "Bitcoin is often referred to as programmable money because it allows for the execution of programmable transactions through its scripting language."
draft = false
[taxonomies]
tags = [ "software", "bitcoin" ]
[extra]
subtitle = "The power of Bitcoin's Script"
static_thumbnail = "/images/blog/2024-07-06/cover.jpg"
+++

![cover](/images/blog/2024-07-06/cover.jpg)

Bitcoin is often referred to as "programmable money" because it allows for the execution of programmable transactions through its scripting language.

<!-- more -->

## Understanding Programmable Money

Programmable money refers to the ability to embed logic and conditions into financial transactions. This programmability allows for transactions to be executed automatically based on predefined rules, without the need for intermediaries or manual intervention. Essentially, it transforms money from a static medium of exchange into a dynamic tool capable of executing complex agreements and automating financial operations.

## Bitcoin's Script Language

Bitcoin's programmability is powered by its built-in scripting language, known as [Script](https://en.bitcoin.it/wiki/Script). Unlike traditional programming languages, Script is a stack-based, Forth-like language designed specifically for Bitcoin transactions.

It supports multi-signature, time-locked, and other conditional transfers that can be programmed into the Bitcoin transactions. It is intentionally not Turing-complete, without loops.

This video includes examples of the most commonly used locking/unlocking scripts.

{{ youtube(id="6Fa04MnURhw") }}

## Key Features of Bitcoin's Script

#### Stack-Based Execution

Script operates on a stack-based execution model where commands and data are pushed onto a stack and processed in a Last-In-First-Out ([LIFO](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))) manner.

#### Conditional Spending

A transaction that can only be spent if certain data is provided or specific criteria are met. This can be used for:
  - escrow services
  - atomic swaps
  - and other complex financial arrangements

### Multisignature

Transactions can be set up to require multiple signatures from different private keys before they can be spent. Useful for:
  - joint accounts
  - corporate funds
  - and enhancing security, as no single party can unilaterally spend the funds

#### Timelocking

Transactions can include time-based conditions that prevent them from being spent until a certain time or block height is reached. This feature is useful for various purposes:
  - delayed payments
  - smart contracts
  - and ensuring that funds are not spent prematurely (eg: with [Lightning Network](https://en.bitcoin.it/wiki/Lightning_Network))

#### OP_Codes

Script ([OP_Codes](https://en.bitcoin.it/wiki/Script#Opcodes)) perform different functions. Here are some of them:

- `OP_DUP`: Duplicates the top stack item.
- `OP_HASH160`: Hashes the top stack twice (SHA-256 followed by RIPEMD-160).
- `OP_EQUALVERIFY`: Verifies that the top two items are equal and removes them.
- `OP_CHECKSIG`: Verifies a signature against a public key.
- [`OP_RETURN`](https://en.bitcoin.it/wiki/OP_RETURN): Marks the transaction output as invalid, often used to store data.

---

## Common Bitcoin address types

In Bitcoin, different address types correspond to various ways to script transactions. Here, we'll explore examples of Bitcoin Script for each major address type. Each address type has its own specific script format.

- [**P2PK**](#p2pk-pay-to-pubkey-earliest-legacy-address-up): Legacy transactions using full public keys directly.
- [**P2PKH**](#p2pkh-pay-to-pubkey-hash-legacy-address-up): Begins with `1`. Standard transactions using public key hashes.
- [**P2SH**](#p2sh-pay-to-script-hash-up): Begins with `3`. Encapsulates complex scripts like multisig.
- [**P2MS**](#p2ms-pay-to-multisig-up): It is typically a type of P2SH or P2WSH address.
- [**P2WPKH**](#p2wpkh-pay-to-witness-public-key-hash-segwit-up): Begins with `bc1`. Native SegWit, more efficient transactions.
- [**P2WSH**](#p2wsh-pay-to-witness-script-hash-segwit-up): Begins with `bc1`. SegWit for complex scripts.
- [**P2TR**](#p2tr-pay-to-taproot-taproot-up): Begins with `bc1p`. SegWit Taproot addresses, improving privacy and efficiency for complex transactions.

> Using native SegWit (P2WPKH and P2WSH) is preferable when possible, as it maximizes the benefits of the SegWit upgrade, but P2SH-SegWit can be useful for compatibility with older systems.

### P2PK (Pay-to-PubKey) - Earliest Legacy Address <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with "1" (e.g., 1A1zP1eP5QGefi2DMPTf...v7DivfNa)
</div>

Before P2PKH and P2SH became standard, Bitcoin addresses were not as flexible or feature-rich. Here are some considerations:

- Format: Addresses starting with 1 but without hashing the public key.
- Usage: Used in early Bitcoin transactions but not common in modern practice due to lack of privacy and efficiency.
- Status: The P2PK format is largely obsolete in favor of (at least) P2PKH and other address types that provide additional features and security.

---

### P2PKH (Pay-to-PubKey-Hash) - Legacy Address <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with "1" (e.g., 1A1zP1eP5QGefi2DMPTf...v7DivfNa)
</div>

A typical P2PKH script consists of two main parts:
1. **ScriptPubKey**: The locking script (also known as the output script) that specifies how funds can be spent.
1. **ScriptSig**: The unlocking script (also known as the input script) that provides the necessary data to unlock the funds. 

##### ScriptPubKey (Locking Script)
```php
OP_DUP OP_HASH160 <PubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

- `OP_DUP`: Duplicates the top stack item (the public key).
- `OP_HASH160`: Hashes the public key with SHA-256 followed by RIPEMD-160.
- `<PubKeyHash>`: The hashed public key (a 20-byte value).
- `OP_EQUALVERIFY`: Checks if the hashed public key matches the hash in the script.
- `OP_CHECKSIG`: Verifies the provided signature against the public key.

##### ScriptSig (Unlocking Script)
```php
<Signature> <PublicKey>
```
- `<Signature>`: The digital signature generated by the private key.
- `<PublicKey>`: The public key corresponding to the address.

---

### P2SH (Pay-to-Script-Hash) <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with "3" (e.g., 3J2BtwzN2GEr6FCP.....81T2eiX8PVHh)
</div>

P2SH scripts are used for more complex scripts. The primary feature is that the address itself encodes a hash of a script, which will be used in the transaction.

##### ScriptPubKey (Locking Script) 

```php
OP_HASH160 <ScriptHash> OP_EQUAL
```

- `OP_HASH160`: Hashes the script with SHA-256 followed by RIPEMD-160.
- `<ScriptHash>`: The hashed script (a 20-byte value).
- `OP_EQUAL`: Checks if the hash matches the provided script hash.

##### ScriptSig (Unlocking Script)

```php
<Signature> <PublicKey> ... <ScriptSig>
```
- `<Signature>`: The digital signature.
- `<PublicKey>`: The public key.
- `<ScriptSig>`: The actual script that matches the script hash, which itself will be executed by the Bitcoin network.

---

### P2MS (Pay-to-Multisig) <small>[up](#common-bitcoin-address-types)</small>

##### Script Format

```php
OP_M <M> <PubKey1> <PubKey2> ... <PubKeyN> OP_N OP_CHECKMULTISIG
```

- `OP_M`: The minimum number of signatures required.
- `<PubKey1>, <PubKey2>, ..., <PubKeyN>`: The public keys involved in the multisignature scheme.
- `OP_N`: The total number of public keys provided.
- `OP_CHECKMULTISIG`: The opcode that verifies the signatures against the provided public keys.

##### ScriptPubKey (Locking Script)

```php
OP_2 <PubKey1> <PubKey2> <PubKey3> OP_3 OP_CHECKMULTISIG
```

This script means that any 2 out of 3 provided public keys are required to sign the transaction for it to be valid.

##### ScriptSig (Unlocking Script)
```php
<Signature1> <Signature2> ... <SignatureN> <SerializedScript>
```

- `<Signature1>, <Signature2>, ..., <SignatureN>`: The public keys.
- `<SerializedScript>`: The serialized script (the same as the locking script but without the `OP_M` and `OP_N`).

<div class="status warning-orange">
<b>NOTE</b>: There is an oddity in CHECKMULTISIG execution. 
<small><a href="#there-is-an-oddity-in-checkmultisig-execution-up">See note at the bottom.</a></small>
</div>

---

### P2WPKH (Pay-to-Witness-Public-Key-Hash) - Segwit <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with "bc1q" (e.g., bc1qf0r2m0ck4psv6yrk9w.....kw8v5rj7ph3)
</div>

P2WPKH is a Segregated Witness (SegWit) address type that uses a different scripting format compared to legacy and P2SH addresses. Simplifies transactions by reducing data size and fees compared to legacy formats.

##### ScriptPubKey (Locking Script)

```php
OP_0 OP_PUSHBYTES_20 <PubKeyHash>
```

- `OP_0`: A single byte (0x00) indicating the version of the script.
- `OP_PUSHBYTES_20`: Pushes 20 bytes (the public key hash) onto the stack.
- `<PubKeyHash>`: The 20-byte hash of the public key.

##### Witness Data

For P2WPKH, the unlocking script is not required in the traditional sense (i.e., inside the unlocking script explicitly included in the transaction input). Instead, the unlocking information is provided as part of the witness data in the SegWit transaction format.

```php
<Signature> <PublicKey>
```

- `<Signature>`: Digital signature for the transaction.
- `<PublicKey>`: Public key used to generate the public key hash.

---

### P2WSH (Pay-to-Witness-Script-Hash) - Segwit <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with bc1q (e.g.: bc1q4a3h5sdg4cfkhftgd24tj9g2sg...yj57jmfckhkrw5gslr9g59)
</div>

##### ScriptPubKey (Locking Script)

```php
OP_0 OP_PUSHBYTES_32 <ScriptHash>
```

- `OP_0`: Indicates a witness version 0 (SegWit).
- `OP_PUSHBYTES_32`: Pushes the next 32 bytes (the script hash) onto the stack.
- `<ScriptHash>`: 32-byte hash of the redeem script.

##### Witness Data

```php
<Signature1> <Signature2> ... <RedeemScript>
```

- `<Signature1>, <Signature2>`: Signatures required to unlock the transaction.
- `<RedeemScript>`: The actual script that matches the script hash. This script will be executed as part of the witness data.

---

### P2TR (Pay-to-Taproot) - Taproot <small>[up](#common-bitcoin-address-types)</small>

<div class="status info">
Begins with bc1p (e.g.: bc1pl9dfv7kvj4hj9s3a8l.....gjstmrpjl09g8ks3ukds70q4r2j5h)
</div>

Taproot combines [Schnorr](https://en.bitcoin.it/wiki/Schnorr) signatures with [MAST](https://en.bitcoin.it/wiki/BIP_0114#Merkelized_Abstract_Syntax_Tree), enabling private, efficient spending conditions and making complex transactions **_appear standard_** unless conditions are revealed. It allows the efficient execution of complex transactions while hiding their details.

##### ScriptPubKey (Locking Script)

```php
OP_1 <x-only PublicKey>
```

- `OP_1`: Indicates a witness version 1 (Taproot).
- `<x-only PublicKey>`: A 32-byte Schnorr public key without the y-coordinate (x-only).

##### Witness Data

```php
<Signature>
```

- `<Signature>`: A Schnorr signature proving possession of the private key corresponding to the x-only pubkey.

---

{{ youtube(id="yU3Sr07Qnxg") }}

---

### *There is an oddity in CHECKMULTISIG execution <small>[up](#scriptsig-unlocking-script-2)</small>

The implementation of `OP_CHECKMULTISIG` pops one more item than it should. The extra item is disregarded when checking the signatures, so it has no direct effect on the OP itself. It must be present because if `OP_CHECKMULTISIG` attempts to pop on an empty stack, it will cause a stack error and script failure.

```php
OP_0 <SigB> <SigC> 2 <Pubkey A> <Pubkey B> <Pubkey C> 3 OP_CHECKMULTISIG
```

The input script in this multisig is not `<SigB> <SigC>` but `OP_0 <SigB> <SigC>`.

It because the custom early on to use `OP_0` which later because a relay policy rule and eventually a consensus rule ([BIP147](https://github.com/bitcoin/bips/blob/master/bip-0147.mediawiki)).

It is possible that the original developer added the extra element in the original version of Bitcoin, so they could add a feature for allowing a map to be passed in a later soft fork (for performance reasons). However, that feature was never implemented, and the BIP147 update to the consensus rules in 2017 makes it impossible to add that feature in the future.

Only Bitcoin's original developer could tell whether the dummy stack element was the result of a bug or a plan for a future upgrade. From now on, if you see a multisig script, you should expect to see an extra `OP_O` in the beginning, whose only purpose is as a workaround to an oddity in the consensus rules.

---

## Follow-ups

- [Bitcoin **IDE**](https://siminchen.github.io/bitcoinIDE/build/editor.html) is an online Bitcoin Script visual emulator. Great for learning purposes.
- [Script **Editor**](https://coins.github.io/bitcoin-scripts/script-editor/) is product-driven research on Bitcoin scalability and usability.
- [**Miniscript**](https://bitcoin.sipa.be/miniscript/) is a language for writing (a subset of) Bitcoin Scripts in a structured way, enabling analysis, composition, generic signing, and more.

### Related readings

- [Mastering Bitcoin](/readings/mastering-bitcoin/) <small>by Andreas M. Antonopoulos, David A. Harding</small>