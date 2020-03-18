---
description: >-
  Meeco's encryption libraries to make encrypting and decrypting data in the
  API-of-Me easy
---

# Cryppo

Cryppo is a cryptographic library that helps you encrypt and decrypt data in way that is compatible with Meeco's recommended serialization and encryption formats.

It comes in two flavours - Ruby and Javascript - but you can re-implement the routines in whatever Language you like. Feel like using Flutter? Write the library in Dart!

Cryptography is hard, and the Cryppo libraries do all the heavy lifting in the creation of cryptographic key pairs, encrypting and decrypting data, and make it trivial to serialize the encrypted data in a way that makes it easy to store and retrieve from the Meeco Vault.

Visit [https://github.com/Meeco/cryppo](https://github.com/Meeco/cryppo) for the Ruby version, and [https://github.com/Meeco/cryppo-js](https://github.com/Meeco/cryppo-js) for the Javascript library. 

We'll be using the libraries to run through some of the more in-depth examples on this documentation site,  so choose your preferred language and follow along.

### Cryppo CLI

We've also created a simple CLI for Cryppo called `cryppo-cli` which can be found at [https://github.com/Meeco/cryppo-cli](https://github.com/Meeco/cryppo-cli)

Using this tool, you can very quickly generate a Data Encryption Key and easily encrypt and decrypt data to save to the slots on an item. 

#### Installation

Clone the repo, run `npm install` and npm link .. This will let you use `cryppo` and it's commands, else you'll have to run `./bin/cryppo`

And that's it! `cryppo-cli` lets you generate [DEKs](../guides/terminology.md#data-encryption-key-dek) and also allows you to encryt and decrypt data with pre-existing DEKs.











