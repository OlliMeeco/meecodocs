---
description: How to create a connection between you and another user to share data
---

# Connections and Sharing

### A follow along guide using the Meeco CLI to build on the Quickstart Guide

After successfully creating an item in your user's Vault from the [Quickstart section](../getting-started/quickstart.md), it's now time to create another user.

```bash
meeco users:create -p supersecretpassword > .user_2.yaml
```

We used the same password as in the [Quickstart](../getting-started/quickstart.md) example, in case you were wondering.

Using the CLI again, we're going to make a connection configuration file between `user` and `user_2`

```bash
meeco connections:create-config --from .user.yaml --to .user_2.yaml > .connection_config.yaml
```

This creates a file called `.connection_config.yaml` which we will open and edit the `fromName` and `toName` keys. You can name the users whatever you like. Next, it's time to use the CLI again to create the connection between the two users.

```bash
meeco connections:create -c .connection_config.yaml
```

This generates the keypairs for the connection, creates and accepts the invitation for the two users.

Now, we're ready to select an item from user's vault and share it with `user2` . First, we'll need to create the share template with the CLI. You can see the items your user has by typing `meeco items:list` in the command line. Once you have have the id, add it to the following CLI command:

```bash
meeco shares:create-config --from .user.yaml --to .user_2.yaml -i 5fc9c712-5abe-4f6a-a551-61c32f2e09f5 > .share_config.yaml
```

After this configuration file is created, we can create the share between the two users:

```bash
meeco shares:create -c .share_config.yaml
```

The CLI sets up a private encryption space between `user` and `user2` and then shares the item.

We never created an item for the second user, so we can see how looking at another user's shared item works:

```bash
meeco shares:list -a .user_2.yaml
```

The above lists all the shares between the users:

```yaml
kind: Shares
spec:
  - share_id: bed6ea54-701e-4d36-9142-a975cad21911
    connection_id: 9123d4ff-2fd3-42c9-bd73-634d8b1792a6
    item:
      id: 5fc9c712-5abe-4f6a-a551-61c32f2e09f5
      name: my_car
      label: My Car
      description: null
      created_at: "2020-03-12T07:26:07.374Z"
      item_template_id: 61516c6f-81df-4c86-96df-8af915f0aec0
      ordinal: 1
      visible: true
      updated_at: "2020-03-12T07:26:08.095Z"
      item_template_label: Vehicle
      shareable: false
      me: false
      background_color: null
      image: https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca
      image_background_colour: null
      item_image: https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca
      item_image_background_colour: null
      slot_image: null
      slot_image_background_colour: null
      activity_image: https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca
      activity_image_background_colour: null
      category_image: null
      category_image_background_colour: null
      classification_node_ids: []
      category_label: null
      association_ids: []
      associations_to_ids: []
      slot_ids:
        - a6e6bc15-1f3a-415d-8e0c-145a64b8227b
        - 1a266560-779f-47e6-b633-7c6697f08ac9
        - f60d4bb1-3a17-4b26-8abc-5ca9f438b74c
        - ca33626a-9e9b-46f3-8177-895fc4d20d2f
        - 18ec155d-4770-4932-9fa5-4582822b5baf
        - 2dc3de7e-080c-4eec-9159-f12bda06fa30
        - a0bc209d-0f49-454c-a262-ab4216770368
        - 66c44e27-c1ed-44cb-8a6d-e50699273f7a
```

We can then grab the `share_id` and use it in the next call:

```yaml
meeco shares:get -a .user_2.yaml bed6ea54-701e-4d36-9142-a975cad21911
```

Well done - you've now created a connection between two users, and shared an item!

## Sharing Cards Between Users - In Depth

All user data stored in the Vault is encrypted and can only be decrypted and read by the user.

Howerver, the Meeco platform makes it possible for one user to share cards with another user. We will cover this process and its steps in this guide.

In summary, the sharer will generate a DEK \(data encryption key\) specifically for the purpose of this share and re-encrypt the shared card with this key. In order to share the DEK, Public Key cryptography is used: the sharer will encrypt the DEK with a Public Key of the share recipient, so only the share recipient can decrypt the DEK with their Private Key, and then use the DEK to decrypt the card.

Let's dive into it.

### Invitation To Connect

Before anything can be shared, 2 Users need to establish a _connection_. In order to create a connection in this example, User 1 will invite User 2.

The process can be described in the following sequence diagram:

At step \(1\) User 1 generates a Keypair which will be used for inviting another user, and later for the key exchange.

Steps 2-4 are part of the standard procedure used for storing Keypairs in the Keystore. If there is a Keypair, it is encrypted by the Key Encryption Key \(KEK\) and stored in the Keystore. Please read guide Setting Up Access to the Vault and Keystore `02.setting_up_access_to_the_vault_and_keystore.md` if you haven't done read it yet.

In steps 5 and 6, User 1 stores the Public Key. In steps 7-9 User 1 creates an invitation using the following as input:

* email of the user that User 1 wants to connect to \(User 2\)
* the ID of the Public Key stored in step 5

In step 8 the Vault sends an invitation email to User 2.

### Accepting Invitation

In this section we'll describe the scenario when User 2 accepts the invitation from User 1.

This process can be described in the following sequence diagram:

Most of these steps are the the same steps of User 1 in the previous section: just like User 1, User 2 generates a Keypair for this connection \(step 11\), encrypts it and stores in the Keystore \(steps 12-14\), and publishes the Public Key in the Vault \(steps 15-16\).

The most important step is a call to create a connection as step 12. The parameters of the call are the invitation ID and the invitation token.

The most important results after these two sections are as follows:

* The connection between User 1 and User 2 has now been established
* User 1 has access to the Public Key of User 2 via the connection record
* User 2 has access to the Public Key of User 1 via the connection record

### Creating A Share

In this section, to create a share, User 1 will generate a DEK dedicated to this share, re-encrypt a card and store it as a share, and share the DEK with User2, encrypted by the Public Key of User 2.

The process of encryption and the key exchange is abstracted into something called _Encryption Space_. An encryption space exists both in the Keystore and the Vault and both records share the same ID. The function of the encryption space in the Keystore is key exchange. The function of the encryption space in the Vault is a way to point to the correct encryption space in the Keystore for a given connection.

Creation of a share can be described in the following sequence diagram:

At step 23 User 1 generates a DEK. This DEK will be used to encrypt the shared card. As we always do with DEKs, we encrypt it the Key Encryption Key \(step24\). But we also need to have the key readable by User 2, so at step 25 we encrypt the same KEK by the Public Key of User 2.

At steps 26 and 27 an encryption space is created in the Keystore, containing two versions of the same DEK, one only readable by User 1, the other one only readable by User 2.

In steps 28-29 an encryption space is created in the Vault with the same ID as the encryption space in the Keystore. The encryption space in the Vault is linked to the connection record.

In steps 30-31 User 1 encrypts the card data with the shared DEK and creates a Share record

The main results of these steps are as follows:

* A DEK has been created waiting to be claimed by User 2
* This DEK is wrapped in an Encryption Space
* A Share record has been created in the Vault, and it is linked to the connection between User 1 and User 2
* An Encryption Space in the Vault is a link between the connection and the Encryption Space in the Keystore

### Reading The Share

Reading of the share can be described in the following sequence diagram:

First in step 33 User 2 retrieves a list of all incoming shares. Each share has a connection ID.

If there is a new share User 2 needs to decrypt and read, in step 35 User 2 requests the connection for the share.

Once the ID of the encryption space is known, User 2 retrieves the DEK in steps 37-38, decrypts it with their Private Key in step 39 and decrypts the share in step 43.

As it is always done with keys, this DEK is encrypted with the Key Encryption Key \(KEK\) of User 2 and stored in the Keystore in steps 40-42

