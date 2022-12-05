---
description: How to create a connection between you and another user to share data
---

# Connections and Sharing

### A follow along guide using the Meeco CLI to build on the Quickstart Guide

_Below the guide using the CLI we have a more in depth explanation of how sharing works_

After successfully creating an item in your user's Vault from the [Quickstart section](../getting-started/quickstart.md), it's now time to create another user called Bob.

```bash
meeco users:create -p supersecretpassword > .bob.yaml
```

We used the same password as in the [Quickstart](../getting-started/quickstart.md) example, in case you were wondering.

Using the CLI again, we're going to make a connection configuration file between _Alice_ and _Bob_

```bash
meeco connections:create-config --from .alice.yaml --to .bob.yaml > .connection_config.yaml
```

This creates a file called `.connection_config.yaml` which we will open and edit the `fromName` and `toName` keys. Let's make it between Alice and Bob. Next, it's time to use the CLI again to create the connection between the two users.

```bash
meeco connections:create -c .connection_config.yaml > .connection.yaml
```

This generates the keypairs for the connection, creates and accepts the invitation for the two users.

Now, we're ready to select an item from Alice's vault and share it with Bob.

First, we'll need to create the share template with the CLI.

```bash
meeco shares:create-config --from .alice.yaml -c .connection.yaml -i .item.yaml > .share_config.yaml
```

After this configuration file is created, we can create the share between the two users:

```bash
meeco shares:create -c .share_config.yaml > .share.yaml
```

The output is a new shares item:

```bash
shares:
  - id: 0f894916-852a-4682-bd49-0783ab58e1c0
    owner_id: ab9f9fce-db0b-4384-a221-617efa80dba7
    sender_id: ab9f9fce-db0b-4384-a221-617efa80dba7
    recipient_id: ce021e77-a66f-4fae-a150-d3a4a6e1a7f9
    acceptance_required: acceptance_not_required
    item_id: bae62ab6-ea95-4037-8f6c-3708c81b2d77
    slot_id: null
    public_key: "-----BEGIN PUBLIC KEY-----\r
    ...
    -----END PUBLIC KEY-----\r\n"
    ...
    sharing_mode: owner
    keypair_external_id: f0ab31a1-c95d-463d-b6b1-1a72e1f56444
    encrypted_dek: Rsa4096.Jm9R1Ve2KcOLc4-HkZkjviB8HXBSlVQLfTlUJ-xcGRRklBp-Od-g2YjareSFwMorzVrtVDKWg8QWkB3iDAn_g9pG3c-kY1Le5Gb86VTO3hhx74jImf_iw29VUUcAsfRQH2u69X5byyYYlg827nMpT8CgN4P3USsMsMMsXrppu7ONGwk-xxItJtr8S3cONECp5L_4cbcR4IDbGBpVGZMdU5X6YU3ZZ7z-fi5wF5tRp6krR4V8rqbJOlyURY2xwj3ihoGtPc6Dbef_H6viFEgl00gyDegXKgJ8IisES_6_cyq7ooiGbux5oTgyg4tTIA40Lf65JLzVujosFC56EatRumR-YretG_Dkr61PQfuGN2zpTOGpZzypnc-HJc-GCHWGLU1wqwhcBY3NNoM1NvmdWGRQV2Vrtt3rhBCM2Nt-E7lCyQTX45qGXG-q-nL2b6l_DfCfp6O5s4hAYVoBQgDLCexl1YFb0reNm1Ol3rQ_hjpPn9LHAgE93Mdq7b04-sBmbNF54oLyrAneZu8NOle1-dioK13dLNooSm_O5MuRdnjyaJZH5zcsN-mEeSzsTHBymiMitet1-YOoZrenLDUaaFpWj6fCgwW6louU7u8PWq8U40TV15c8TndQAVFyRhfPav8HHLhOJmOCa1HaqdGZ8vuw1efJW3rtOU2ye31JQIw=.QQUAAAAA
    terms: null
    created_at: 2020-09-24T07:15:03.315Z
    expires_at: null
```

The CLI sets up a _private encryption space_ between Alice and Bob and then shares the item.

We never created an item for the Bob, so we know that the following command will show the item that has been shared with Bob.

```bash
meeco shares:get-incoming -a .bob.yaml <SHARE_ID>
```

The following is the share information, as well as the item that was shared:

```bash
share:
  id: 0f894916-852a-4682-bd49-0783ab58e1c0
  owner_id: ab9f9fce-db0b-4384-a221-617efa80dba7
  sender_id: ab9f9fce-db0b-4384-a221-617efa80dba7
  recipient_id: ce021e77-a66f-4fae-a150-d3a4a6e1a7f9
  acceptance_required: acceptance_not_required
  item_id: bae62ab6-ea95-4037-8f6c-3708c81b2d77
  slot_id: null
  ...
    sharing_mode: owner
  keypair_external_id: f0ab31a1-c95d-463d-b6b1-1a72e1f56444
  encrypted_dek: Rsa4096.Jm9R1Ve2KcOLc4-HkZkjviB8HXBSlVQLfTlUJ-xcGRRklBp-Od-g2YjareSFwMorzVrtVDKWg8QWkB3iDAn_g9pG3c-kY1Le5Gb86VTO3hhx74jImf_iw29VUUcAsfRQH2u69X5byyYYlg827nMpT8CgN4P3USsMsMMsXrppu7ONGwk-xxItJtr8S3cONECp5L_4cbcR4IDbGBpVGZMdU5X6YU3ZZ7z-fi5wF5tRp6krR4V8rqbJOlyURY2xwj3ihoGtPc6Dbef_H6viFEgl00gyDegXKgJ8IisES_6_cyq7ooiGbux5oTgyg4tTIA40Lf65JLzVujosFC56EatRumR-YretG_Dkr61PQfuGN2zpTOGpZzypnc-HJc-GCHWGLU1wqwhcBY3NNoM1NvmdWGRQV2Vrtt3rhBCM2Nt-E7lCyQTX45qGXG-q-nL2b6l_DfCfp6O5s4hAYVoBQgDLCexl1YFb0reNm1Ol3rQ_hjpPn9LHAgE93Mdq7b04-sBmbNF54oLyrAneZu8NOle1-dioK13dLNooSm_O5MuRdnjyaJZH5zcsN-mEeSzsTHBymiMitet1-YOoZrenLDUaaFpWj6fCgwW6louU7u8PWq8U40TV15c8TndQAVFyRhfPav8HHLhOJmOCa1HaqdGZ8vuw1efJW3rtOU2ye31JQIw=.QQUAAAAA
  terms: null
  created_at: 2020-09-24T07:15:03.315Z
  expires_at: null
associations_to: []
associations: []
attachments: []
classification_nodes:
  - id: 8670d4c6-8d68-49a4-bd21-0fc8cefa705d
    name: vehicle
    label: Vehicle
    description: null
    ordinal: 3
    background_color: null
    image: https://sandbox.meeco.me/vault/images/ff1c25e9-530a-4103-b649-986631bcAAAAA
    scheme: meeco
item:
  id: a3f632c8-f80f-47aa-9e26-aab15ad9ed63
  own: false
  name: a_new_item
  label: A New Item
  description: null
  created_at: 2020-09-24T07:15:03.452Z
  item_template_id: 0c385f1d-8825-4932-a6ab-846178b816e4
  ordinal: 0
  visible: true
  updated_at: 2020-09-24T07:15:03.493Z
  ...
```

Running `meeco shares:list -a .bob.yaml` will show all the shares information that Bob has received, even from other users. 

`meeco shares:list -t outgoing -a .alice.yaml` will show all the shares that are outgoing from Alice to other users.

If you're looking for a way to delete the share, you can do that as either user with `meeco shares:delete -a .alice.yaml <SHARE_ID>` or `meeco shares:delete -a .bob.yaml <SHARE_ID>`
 
Well done - you've now created a connection between two users, and shared an item!

## Sharing Items Between Users - In Depth

All user data stored in the Vault is encrypted and can only be decrypted and read by the user.

However, the Meeco platform makes it possible for one user to share items with another user. We will cover this process and its steps in this guide.

In summary, the sharer will generate a DEK \(data encryption key\) specifically for the purpose of this share and re-encrypt the shared item with this key. In order to share the DEK, Public Key cryptography is used: the sharer will encrypt the DEK with a Public Key of the share recipient, so only the share recipient can decrypt the DEK with their Private Key, and then use the DEK to decrypt the item.

![](../.gitbook/assets/section3.png)

Let's dive into it.

### Invitation To Connect

Before anything can be shared, 2 Users need to establish a _**connection**_. In order to create a connection in this example, User 1 \(Alice\) will invite User 2 \(Bob\)

The process can be described in the following sequence diagram:

![](../.gitbook/assets/send_invitation.png)

At step \(1\) User 1 generates a Keypair which will be used for inviting another user, and later for the key exchange.

Steps 2-4 are part of the standard procedure used for storing Keypairs in the Keystore. If there is a Keypair, it is encrypted by the Key Encryption Key \(KEK\) and stored in the Keystore. Please read guide [Setting Up Access to the Vault and Keystore](setting-up-access.md) if you haven't read it yet.

In steps 5 and 6,

 stores the Public Key. In steps 5-7 User 1 creates an invitation using the following as input:

* email of the user that User 1 wants to connect to \(User 2\)
* the Public Key

After step 7 the Vault sends an invitation email to User 2.

### Accepting Invitation

In this section we'll describe the scenario when User 2 accepts the invitation from User 1.

This process can be described in the following sequence diagram:

![](../.gitbook/assets/accept_invitation.png)

Most of these steps are the the same steps of User 1 in the previous section: just like User 1, User 2 generates a Keypair for this connection \(step 9\), encrypts it and stores in the Keystore \(steps 10-12\), and publishes the Public Key in the Vault \(steps 13-14\).

The most important step is a call to create a connection as step 13. The parameters of the call are the invitation ID and the invitation token.

The most important results after these two sections are as follows:

* The connection between User 1 and User 2 has now been established
* User 1 has access to the Public Key of User 2 on the connection record
* User 2 has access to the Public Key of User 1 on the connection record

### Creating A Share

In this section, to create a share, User 1 will generate a DEK dedicated to this share, re-encrypt a item and store it as a share, and share the DEK with User2, encrypted by the Public Key of User 2.

Creation of a share can be described in the following sequence diagram:

![](../.gitbook/assets/create_share.png)

At step 19 User 1 generates a DEK. This DEK will be used to encrypt the shared item. We also need to have the key readable by User 2, so at step 20 we encrypt the same DEK with the Public Key of User 2.

In steps 21-23 User 1 encrypts the item data with the shared DEK and creates a Share record.

The main results of these steps are as follows:

* A DEK has been created and encrypted with User 2's public key
* A Share record has been created in the Vault with the encrypted DEK, and it is linked to the connection between User 1 and User 2

### Reading The Share

Reading of the share can be described in the following sequence diagram:

![](../.gitbook/assets/read_share.png)

First in step 24 User 2 retrieves a list of all items both his own and shared incoming.

If there is a new share User 2 needs to decrypt and read, in step 26 User 2 requests the share details.

User 2 also retrieves the DEK in steps 26-27, decrypts it with their Private Key in step 28 and decrypts the share in step 29.
