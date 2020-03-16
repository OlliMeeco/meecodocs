# Terminology

## Vault

Storage for encrypted user data. User data cannot be decrypted and read by Meeco. In the current implementation the _**Vault**_ functionality is implemented by the `meeco-api`.

## Keystore

Storage for secrets and keys. In the current implementation the **Keystore** functionality is implemented by `meeco-keystore`.

## Slot

A _**Slot**_ in the smallest data entity in the _**Vault**_. A _**Slot**_ is a placeholder for one data value. Each _**Slot**_ has a `name`, a `label`, and a `value`. _**Slots**_ are typed. A _**Slot**_ type defines what can be stored in a _**Slot**_ and how this data is handled. Example _**Slot**_ types are:

* `bool`
* `date`
* `datetime`
* `image`
* `key_value`
* `url`
* `phone_number`
* `email`
* `password`

_**Slot**_ values are stored in an encrypted form and only the user can read them.

## Item

An _**Item**_ is a group of _**Slots**_ related by a topic. For example, a user profile is an _**Item**_. A club membership, a flight reservation - all these can be _**Items**_ each having a number of _**Slots**_ of different tiles in them.

## An Item Template

An _**Item Template**_ is a predefined list of empty _**Slots**_. Each _**Item**_ is created by cloning such a template and filling in the _**Slots**_.

## Connection

A _**Connection**_ between two users is a channel via which users can share individual _**Slots**_ on the _**Items**_, or the entire _**Item**_ itself.

## Share

A _**Share**_ is created when a user grants access to their _**Item**_ to another user. The _**Item**_ is re-encrypted with a data encryption key shared with the recipient of the _**Share**_.

## Passphrase Derived Key And Derivation Artefacts

A _**Passphrase Derived Key**_ is a [`PBKDF2`](https://en.wikipedia.org/wiki/PBKDF2) key in the current implementation. To generate or re-generate this key, a passphrase and derivation artefacts are required. Derivation artefacts include:

* Number of iterations
* Salt
* Derived key length

Derivation artefacts are stored in the _**Keystore**_. Neither the _**Passphrase Derived Key**_ itself nor the passphrase are stored in the _**Keystore**_

## Key Encryption Key \(KEK\)

The _**Key Encryption Key**_ which is used to encrypt all other keys \(data encryption keys and keypairs\) before they are stored in the _**Keystore**_. The _**Key Encryption Key**_ is encrypted with the _**Password Derived Key**_.

In the current implementation this is an [`AES256-GCM`](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) key, but the serialization format of encrypted data used in the Meeco platform allows for adding new encryption algorithms without breaking backwards compatibility.

There is one _**Key Encryption Key**_ per user.

## Data Encryption Key \(DEK\)

_**Data Encryption Keys**_ are `AES256-GCM` keys used to encrypted and decrypt user data. _**Data Encryption Keys**_ are stored in the **Keystore** encrypted with the _**Key Encryption Key**_.

A _**Data Encryption Key**_ is created for various functions of the API. For instance, a DEK is created when a user _**Shares**_ a _**Slot**_ with another user. When that user connects with another user, the first time they share another DEK is created.

It is possible for a user to have multiple _**Data Encryption Keys**_

## Keypair

Public key cryptography is used for _**Connections**_ and shared between users. Private keys are stored in the _**Keystore**_ encrypted with the _**Key Encryption Key**_.

## Classification Scheme

The Meeco platform has a very flexible way to tag information. Instead of having a traditional simple flat list of tags the system can be configured to have multiple independent _**Classifications**_. Combinations of these _**Classifications**_ are called _**Classification Schemes**_.

## Classification Node

A _**Classification Scheme**_ consists of a tree of _**Classification Nodes**_. A _**Classification Node**_

* belongs to a _**Classification Scheme**_
* has a parent _**Classification Node**_ unless it is the top node
* has property `name`
* has property `label`
* has property `description`
* has property `image`

## Classification

A _**Classification**_ is a link between a _**Classification Node**_ and a classified entity. The link to a classified entity is polymorphic, that is, many entities can be classified in the Meeco model.

