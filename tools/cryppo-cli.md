# Cryppo CLI

We've created a simple CLI for Cryppo called `cryppo-cli` which can be found at [https://github.com/Meeco/cryppo-cli](https://github.com/Meeco/cryppo-cli)

Using this tool, you can very quickly generate a Data Encryption Key and easily encrypt and decrypt data to save to the slots on an item. 

#### Installation

Clone the repo, run `npm install` and npm link .. This will let you use `cryppo` and it's commands, else you'll have to run `./bin/cryppo`

`cryppo-cli` allows you to do the following:

* Generate a random encryption key with a default of 32 character length. You can change the length of the key with the `-l` flag
* Encrypt a string with the the key that was generated, or an existing key that user has in the Meeco Keystore. The result is a serialized and encoded websafe string.
* Decrypt with a key a serialized and encoded string.

And that's it! `cryppo-cli` lets you generate [DEKs](../guides/terminology.md#data-encryption-key-dek) and also allows you to encrypt and decrypt data with pre-existing DEKs.

This tool is used in some of our examples in the documentation, so by all means download it and get it up and running before moving onto the sections. 

