# Terminology

## Classification

A _**Classification**_ is a link between a [Classification Node](#classification-node) and a classified entity.
[Items](#item), [Slots](#slot) and [Templates](#item-template) can have Classifications.

## Classification Node

A [Classification Scheme](#classification-scheme) consists of a tree of _**Classification Nodes**_. A Classification Node

* belongs to a Classification Scheme
* has a parent Classification Node, unless it is the root node
* has property `name`
* has property `label`
* has property `description`
* has property `image`

## Classification Scheme

The Meeco platform has a very flexible way to tag information. Instead of having a flat list of tags the system can be configured to have multiple independent [Classifications](#classification). Combinations of these Classifications are called _**Classification Schemes**_.

## Connection

A _**Connection**_ between two users is a channel via which users can share [Items](#item).
It is essentially a pair of [public keys](#keypair) (yours and the other party's), and user ids.

You can read a more detailed explanation of Sharing Items [here](connections-and-sharing.md), and you can run through creating a Connection and sharing an item using the Meeco CLI tool [here](connections-and-sharing.md). (Make sure you've gone through the [Quickstart guide](../getting-started/quickstart.md) first to have gained access to the API sandbox!)

## Data Encryption Key \(DEK\)

_**Data Encryption Keys**_ are [`AES256-GCM`](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) keys used to encrypted and decrypt user data. Data Encryption Keys are stored in the [Keystore](#keystore) encrypted with the Key Encryption Key.

It is possible for a user to have multiple Data Encryption Keys: there is one that is used to encrypt your private data; new Keys are created for any Shares, so that other users never see your private DEK.

## Item

An _**Item**_ is a group of [Slots](#slot) related by a topic. For example, a user profile is an Item. A club membership, a flight reservation - all these can be Items, each having a number of Slots of different types in them.

The Slots in an Item are keyed by their name property, so an Item can be thought of like a dictionary or hash-map containing only encrypted values.

If a user makes a [Connection](#connection) with another user, they can share the encrypted slots with that user.

## Item Template

An _**Item Template**_ is a predefined list of empty [Slots](#slot). Each [Item](](#item) is created by cloning such a template and filling in the Slots with your data.

You can read a more detailed document about Items and Templates [here](items-and-slots.md)

## Key Encryption Key \(KEK\)

The _**Key Encryption Key**_ is used to encrypt all other keys \(data encryption keys and keypairs\) before they are stored in the [Keystore](#keystore).
The Key Encryption Key is encrypted with the [Passphrase Derived Key](#passphrase-derived-key-and-derivation-artefacts), which is private to the user.

In the current implementation this is an [`AES256-GCM`](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) key, but the serialization format of encrypted data used in the Meeco platform allows for adding new encryption algorithms without breaking backwards compatibility.

There is one Key Encryption Key per user.

## Keypair

Public key cryptography is used for exchanging [DEKs](#data-encryption-key-dek) when [Connections](#connection) are created between users.
Private keys are stored in the [Keystore](#keystore) encrypted with the [Key Encryption Key](#key-encryption-key-key).

## Keystore

Storage for secrets and keys. This is where the [Data Encryption Keys](#data-encryption-key-dek), [Public/Private Key pairs](#keypair), and the [Key Encryption Key](terminology.md#key-encryption-key-kek), are stored along with the [Derivation Artefacts](#passphrase-derived-key-and-derivation-artefacts).
All of the stored keys are encrypted with the KEK, except for KEK itself, which is encrypted with the Passphrase Derived Key.

No encryption is done in the Keystore, the Cryppo library is required to create and use keys.

In the [Meeco Developer Portal](https://dev.meeco.me) the Keystore is reachable through the `https://sandbox.meeco.me/keystore` endpoint.

## Passphrase Derived Key And Derivation Artefacts

A _**Passphrase Derived Key**_ is a [`PBKDF2`](https://en.wikipedia.org/wiki/PBKDF2) key.
To generate or re-generate this key, a passphrase and derivation artefacts are required. Derivation artefacts include:

* Number of iterations
* Salt
* Derived key length

In the current iteration of our [_Secret Key_](terminology.md#secret-key) authentication and passphrase derivation the number of keys `Number of iterations` and `Derived key length` are static and the Salt is pulled from the Secret Key.

Derivation artefacts are stored in the [Keystore](#keystore). Neither the Passphrase Derived Key itself nor the passphrase are stored in the Keystore.

## Secret Key

The secret key is a component of the authentication flow.

The format for version 1 is as follows:

```bash
{version}-{username}-{salt}
```

* The `username` is generated by the server
* The `salt` is a 256 bit randomly generated key, which is base58 encoded and has a hypen \(`-`\) at each 6th character.

The salt component created on the client and stored \(securely\) by the user.
It is used to generate
1. an encryption key ([PDK](#passphrase-derived-key-and-derivation-artefacts)) with which to encrypt your [Key Encryption Key (KEK)](#key-encryption-key-kek).
2. a password which, along with a username, will be used for [Secure Remote Password (SRP)](#srp---secure-remote-password) authentication.

## Share

A _**Share**_ is created when a user grants access to their [Item](#item) to another user that they've [Connected](#connection) with.
The Item is re-encrypted with a [data encryption key](#data-encryption-key-dek) shared with the recipient of the Share.

An Item you have received via a Share, can be shared to another user, but you cannot alter any of its Slots.
Only the original creator of the Item can update the Share, other than deleting it.

For a detailed look at Sharing and Connections, have a look at the Connections and Sharing Guide, or read through the tutorial for creating a Connection and sharing an item using the [Meeco CLI](../tools/meeco-cli.md) tool [here](connections-and-sharing.md)

## Slot

A _**Slot**_ in the smallest data entity in the [Vault](#vault). An [Item](#item) is made up of Slots, which are keyed by their `name` property.
Each Slot has a `name`, a `label`, and a `value`. Note that the API does not _return_ the `value` property, but `encrypted_value`.
The API will not allow storing any unencrypted data in either `value` or `encrypted_value`.

Slot values are always stored in an encrypted form and only the user can decrypt and read them.
Once encrypted and serialized - you can use one of Meeco's [Cryppo](../tools/cryppo.md) family of encryption libraries - a Slot value of "BMW" would look something like this:

```bash
"encrypted_value": "Aes256Gcm.2hDl.LS0tCml2OiAhYmluYXJ5IHwtCiAgQWQwSThDZk5qRnFycmFuMAphdDogIWJpbmFyeSB8LQogIDJXVklzbUxOSWVoOHZIVDB1ZzBtZVE9PQphZDogbm9uQQo="
```

Slots are typed, however the values cannot be checked that they match the given type, as the API does not have decrypted keys for these items.
Example Slot types are:

* `key_value`
* `bool`
* `date`
* `datetime`
* `image`
* `url`
* `phone_number`
* `email`
* `password`
* `attachment`

Notice that new types cannot be created; `key_value` should be the default type used.

Slots are able to be shared after two users have made a [Connection](#connection) with each other.

## SRP - Secure Remote Password

An authentication method which sends proof that a user knows their password without revealing the actual password to the server.

You can read more about it here - [https://en.wikipedia.org/wiki/Secure\_Remote\_Password\_protocol](https://en.wikipedia.org/wiki/Secure_Remote_Password_protocol)

## Vault

The Vault is where a User of the API-of-Me will store and share the [Items](#item) they create.

The user's data is end-to-end encrypted, this means that the users data cannot be decrypted and read by anyone at Meeco. Your data is _your_ data.

In the [Meeco Developer Portal](https://dev.meeco.me) the Vault is reachable through the `https://sandbox.meeco.me/vault` endpoint.
