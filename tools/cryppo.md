---
description: >-
  Meeco's encryption libraries to make encrypting and decrypting data in the
  API-of-Me easy
---

# Cryppo SDK & CLI

## Cryppo SDK

Cryppo is a cryptographic library that helps you encrypt and decrypt data in way that is compatible with Meeco's recommended serialization and encryption formats.

It comes in different flavours but you can re-implement the routines in whatever Language you like. Feel like using Flutter? Write the library in Dart!

Cryptography is hard, and the Cryppo libraries do all the heavy lifting in the creation of cryptographic key pairs, encrypting and decrypting data, and make it trivial to serialize the encrypted data in a way that makes it easy to store and retrieve from the Meeco Vault.

The library is available for the following platforms

| Language | URL |
| :--- | :--- |
| Elixir | [https://github.com/leikind/cryppo\_ex](https://github.com/leikind/cryppo_ex) |
| Javascript | [https://github.com/Meeco/cryppo-js](https://github.com/Meeco/cryppo-js) |
| Ruby | [https://github.com/Meeco/cryppo](https://github.com/Meeco/cryppo) |

We'll be using the libraries to run through some of the more in-depth examples on this documentation site, so choose your preferred language and follow along.

## Cryppo CLI

We've also created a simple CLI for Cryppo called `cryppo-cli` which can be found at:

{% embed url="https://github.com/Meeco/cryppo" %}

The intro page for this useful tool can be found [here]() on the documentation site.

With the Cryppo CLI you can use the following functions:

* Encrypt a value
* Decrypt a value
* Generate a url-safe base64 key of variable length \(see [DEK](https://docs.meeco.me/guides/terminology#data-encryption-key-dek)\)
* Generate an RSA keypair
* Sign a file with an RSA private key
* Verify a file that has been signed with an RSA key

