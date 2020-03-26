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
meeco connections:create -c .connection_config.yaml
```

This generates the keypairs for the connection, creates and accepts the invitation for the two users.

Now, we're ready to select an item from Alice's vault and share it with Bob. 

First, we'll need to create the share template with the CLI. You can see the items Alice has by useing the `items:list` command:

```yaml
meeco items:list -a .alice.yaml
```

This will give you all the information about the items Alice has in her vault. The response will look like this:

```yaml
kind: Items
spec:
  - id: ed6ac62d-4f9e-4ec3-94f4-9bd1166942ce
    name: delorean
    label: DeLorean
    description: null
    created_at: "2020-03-25T09:31:19.959Z"
    item_template_id: 61516c6f-81df-4c86-96df-8af915f0aec0
    ordinal: 1
    visible: true
    updated_at: "2020-03-25T09:31:20.701Z"
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
    category_image: null
    category_image_background_colour: null
    classification_node_ids: []
    category_label: null
    association_ids: []
    associations_to_ids: []
    slot_ids:
      - 00e99d0d-d1be-469c-aa91-71e9f47333b1
      - 38d6b94c-a3da-4b82-942f-5b7941e0bd7d
      - 94e422b9-b0fb-4b36-bfa9-f2234bf03d69
      - ec6c3f97-b806-4bed-9da3-46a81635c0e3
      - 2c833498-9dfa-4667-94cc-d39c719cab86
      - b583b85e-4e98-49b5-adcd-185b31cc8d90
      - bb1ff70c-6bf6-47ba-9a8a-d916740c55a6
      - f24572c4-0d7b-404d-82f5-8fec593592fc
      - 08471b2d-ac3f-44bc-8932-4faa81366178
```

Once you have have the `id` of the item, add it to the following CLI command:

```bash
meeco shares:create-config --from .alice.yaml --to .bob.yaml -i ITEM_ID_TO_SHARE > .share_config.yaml
```

After this configuration file is created, we can create the share between the two users:

```bash
meeco shares:create -c .share_config.yaml
```

The CLI sets up a _private encryption space_ between Alice and Bob and then shares the item.

We never created an item for the Bob, so we know that the following command will show the items that have been shared with Bob.

```bash
meeco shares:list -a .bob.yaml
```

The above lists all the shares between the users:

```yaml
kind: Shares
spec:
  - share_id: 1f4f9009-7a3a-43ee-b9f2-71d726919d5c
    connection_id: 8c50ec41-acf4-4441-983f-cb8d6f21743c
    item:
      id: ed6ac62d-4f9e-4ec3-94f4-9bd1166942ce
      name: delorean
      label: DeLorean
      description: null
      created_at: "2020-03-25T09:31:19.959Z"
      item_template_id: 61516c6f-81df-4c86-96df-8af915f0aec0
      ordinal: 1
      visible: true
      updated_at: "2020-03-25T09:31:20.701Z"
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
      category_image: null
      category_image_background_colour: null
      classification_node_ids: []
      category_label: null
      association_ids: []
      associations_to_ids: []
      slot_ids:
        - 00e99d0d-d1be-469c-aa91-71e9f47333b1
        - 38d6b94c-a3da-4b82-942f-5b7941e0bd7d
        - 94e422b9-b0fb-4b36-bfa9-f2234bf03d69
        - ec6c3f97-b806-4bed-9da3-46a81635c0e3
        - 2c833498-9dfa-4667-94cc-d39c719cab86
        - b583b85e-4e98-49b5-adcd-185b31cc8d90
        - bb1ff70c-6bf6-47ba-9a8a-d916740c55a6
        - f24572c4-0d7b-404d-82f5-8fec593592fc
        - 08471b2d-ac3f-44bc-8932-4faa81366178
```

We can then grab the `share_id` and use it in the next call:

```yaml
meeco shares:get -a .bob.yaml SHARE_ID
```

Here's the shared DeLorean:

```yaml
kind: Item
spec:
  id: ed6ac62d-4f9e-4ec3-94f4-9bd1166942ce
  name: delorean
  label: DeLorean
  description: null
  created_at: "2020-03-25T09:31:19.959Z"
  item_template_id: 61516c6f-81df-4c86-96df-8af915f0aec0
  ordinal: 1
  visible: true
  updated_at: "2020-03-25T09:31:20.701Z"
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
  category_image: null
  category_image_background_colour: null
  classification_node_ids: []
  category_label: null
  association_ids: []
  associations_to_ids: []
  slot_ids:
    - 00e99d0d-d1be-469c-aa91-71e9f47333b1
    - 38d6b94c-a3da-4b82-942f-5b7941e0bd7d
    - 94e422b9-b0fb-4b36-bfa9-f2234bf03d69
    - ec6c3f97-b806-4bed-9da3-46a81635c0e3
    - 2c833498-9dfa-4667-94cc-d39c719cab86
    - b583b85e-4e98-49b5-adcd-185b31cc8d90
    - bb1ff70c-6bf6-47ba-9a8a-d916740c55a6
    - f24572c4-0d7b-404d-82f5-8fec593592fc
    - 08471b2d-ac3f-44bc-8932-4faa81366178
  slots:
    - id: 00e99d0d-d1be-469c-aa91-71e9f47333b1
      name: power
      created_at: "2020-03-25T09:31:20.679Z"
      updated_at: "2020-03-25T09:31:20.679Z"
      value: 132 PS
      label: Power
      encrypted_value: Aes256Gcm.QAYRmdDe.LS0tCml2OiAhYmluYXJ5IHwtCiAgTzhXa0pQdFE5R1haNURUcQphdDogIWJpbmFyeSB8LQogIFhSaUJYVmtWVW93ak1DTGUyTFRPRHc9PQphZDogbm9uZQo=
    - id: 38d6b94c-a3da-4b82-942f-5b7941e0bd7d
      name: model_make
      created_at: "2020-03-25T09:31:20.174Z"
      updated_at: "2020-03-25T09:31:20.660Z"
      value: DMC DeLorean
      label: Make or model
      encrypted_value: Aes256Gcm.XYQwMPJurLuPIDHR.LS0tCml2OiAhYmluYXJ5IHwtCiAgc3E1RXJIZVN3TVRkUTFrOAphdDogIWJpbmFyeSB8LQogIDlrWGhSQWhIbVdjdUNVemY1NDlIRlE9PQphZDogbm9uZQo=
    - id: 94e422b9-b0fb-4b36-bfa9-f2234bf03d69
      name: licence_plate
      created_at: "2020-03-25T09:31:20.017Z"
      updated_at: "2020-03-25T09:31:20.633Z"
      value: 1AAA999
      label: Vehicle registration number
      encrypted_value: Aes256Gcm.HIcPWNbEaA==.LS0tCml2OiAhYmluYXJ5IHwtCiAgdzcyM09PWnJZTGtITi9DRQphdDogIWJpbmFyeSB8LQogIDVEZVNxRWRoNitURkVMZmRCVWV4a2c9PQphZDogbm9uZQo=
    - id: ec6c3f97-b806-4bed-9da3-46a81635c0e3
      name: vin
      created_at: "2020-03-25T09:31:20.085Z"
      updated_at: "2020-03-25T09:31:20.604Z"
      value: "123456789"
      label: VIN
      encrypted_value: Aes256Gcm.qEdyalKPXS75.LS0tCml2OiAhYmluYXJ5IHwtCiAgQzBIUDhCdGh6SStKZC9OdQphdDogIWJpbmFyeSB8LQogIGlqS1lPNDlQOTVXemJHQ0NNcGZjWlE9PQphZDogbm9uZQo=
    - id: 2c833498-9dfa-4667-94cc-d39c719cab86
      name: image
      created_at: "2020-03-25T09:31:20.382Z"
      updated_at: "2020-03-25T09:31:20.382Z"
      value: null
      label: Image
      encrypted_value: null
    - id: b583b85e-4e98-49b5-adcd-185b31cc8d90
      name: tags
      created_at: "2020-03-25T09:31:20.332Z"
      updated_at: "2020-03-25T09:31:20.332Z"
      value: null
      label: Tags
      encrypted_value: null
    - id: bb1ff70c-6bf6-47ba-9a8a-d916740c55a6
      name: story_category
      created_at: "2020-03-25T09:31:20.297Z"
      updated_at: "2020-03-25T09:31:20.297Z"
      value: null
      label: Category
      encrypted_value: null
    - id: f24572c4-0d7b-404d-82f5-8fec593592fc
      name: type
      created_at: "2020-03-25T09:31:20.218Z"
      updated_at: "2020-03-25T09:31:20.576Z"
      value: Sports Car
      label: Type
      encrypted_value: Aes256Gcm.hS5iJt0KvmoJhA==.LS0tCml2OiAhYmluYXJ5IHwtCiAgOEVlejhVQy9WRUxjd0g5MwphdDogIWJpbmFyeSB8LQogIDZrYk5Gb3hkSFhGNlNHNnhHV1V3a0E9PQphZDogbm9uZQo=
    - id: 08471b2d-ac3f-44bc-8932-4faa81366178
      name: purchase_date
      created_at: "2020-03-25T09:31:20.131Z"
      updated_at: "2020-03-25T09:31:20.547Z"
      value: 01/01/1981
      label: Date purchased
      encrypted_value: Aes256Gcm.te5KGY2qJ2pIRw==.LS0tCml2OiAhYmluYXJ5IHwtCiAgc1Nqd0NZb1dEUlM3b2JQNQphdDogIWJpbmFyeSB8LQogIG5PRGhQVmtFVDZLeStEZnRLOHZ3NGc9PQphZDogbm9uZQo=

Fetching connection... done
```

Well done - you've now created a connection between two users, and shared an item!

## Sharing Cards Between Users - In Depth

All user data stored in the Vault is encrypted and can only be decrypted and read by the user.

Howerver, the Meeco platform makes it possible for one user to share cards with another user. We will cover this process and its steps in this guide.

In summary, the sharer will generate a DEK \(data encryption key\) specifically for the purpose of this share and re-encrypt the shared card with this key. In order to share the DEK, Public Key cryptography is used: the sharer will encrypt the DEK with a Public Key of the share recipient, so only the share recipient can decrypt the DEK with their Private Key, and then use the DEK to decrypt the card.

![](../.gitbook/assets/section3.png)

Let's dive into it.

### Invitation To Connect

Before anything can be shared, 2 Users need to establish a _**connection**_. In order to create a connection in this example, User 1 \(Alice\) will invite User 2 \(Bob\)

The process can be described in the following sequence diagram:

![](../.gitbook/assets/send_invitation_experian.png)

At step \(1\) User 1 generates a Keypair which will be used for inviting another user, and later for the key exchange.

Steps 2-4 are part of the standard procedure used for storing Keypairs in the Keystore. If there is a Keypair, it is encrypted by the Key Encryption Key \(KEK\) and stored in the Keystore. Please read guide [Setting Up Access to the Vault and Keystore](setting-up-access.md) if you haven't read it yet.

In steps 5 and 6,

 stores the Public Key. In steps 7-9 User 1 creates an invitation using the following as input:

* email of the user that User 1 wants to connect to \(User 2\)
* the ID of the Public Key stored in step 5

In step 8 the Vault sends an invitation email to User 2.

### Accepting Invitation

In this section we'll describe the scenario when User 2 accepts the invitation from User 1.

This process can be described in the following sequence diagram:

![](../.gitbook/assets/accept_invitation_experian.png)

Most of these steps are the the same steps of User 1 in the previous section: just like User 1, User 2 generates a Keypair for this connection \(step 11\), encrypts it and stores in the Keystore \(steps 12-14\), and publishes the Public Key in the Vault \(steps 15-16\).

The most important step is a call to create a connection as step 12. The parameters of the call are the invitation ID and the invitation token.

The most important results after these two sections are as follows:

* The connection between User 1 and User 2 has now been established
* User 1 has access to the Public Key of User 2 via the connection record
* User 2 has access to the Public Key of User 1 via the connection record

### Creating A Share

In this section, to create a share, User 1 will generate a DEK dedicated to this share, re-encrypt a card and store it as a share, and share the DEK with User2, encrypted by the Public Key of User 2.

The process of encryption and the key exchange is abstracted into something called _Encryption Space_. An encryption space exists both in the _**Keystore**_ and the _**Vault**_ and both records share the same ID. The function of the encryption space in the _**Keystore**_ is key exchange. The function of the encryption space in the _**Vault**_ is a way to point to the correct encryption space in the _**Keystore**_ for a given connection.

Creation of a share can be described in the following sequence diagram:

![](../.gitbook/assets/create_share_experian.png)

At step 23 User 1 generates a DEK. This DEK will be used to encrypt the shared card. As we always do with DEKs, we encrypt it the Key Encryption Key \(step24\). But we also need to have the key readable by User 2, so at step 25 we encrypt the same KEK by the Public Key of User 2.

At steps 26 and 27 an encryption space is created in the _**Keystore**_, containing two versions of the same DEK, one only readable by User 1, the other one only readable by User 2.

In steps 28-29 an encryption space is created in the Vault with the same ID as the encryption space in the Keystore. The encryption space in the Vault is linked to the connection record.

In steps 30-31 User 1 encrypts the card data with the shared DEK and creates a Share record.

The main results of these steps are as follows:

* A DEK has been created waiting to be claimed by User 2
* This DEK is wrapped in an Encryption Space
* A Share record has been created in the Vault, and it is linked to the connection between User 1 and User 2
* An Encryption Space in the Vault is a link between the connection and the Encryption Space in the Keystore

### Reading The Share

Reading of the share can be described in the following sequence diagram:

![](../.gitbook/assets/read_share_experian.png)

First in step 33 User 2 retrieves a list of all incoming shares. Each share has a connection ID.

If there is a new share User 2 needs to decrypt and read, in step 35 User 2 requests the connection for the share.

Once the ID of the encryption space is known, User 2 retrieves the DEK in steps 37-38, decrypts it with their Private Key in step 39 and decrypts the share in step 43.

As it is always done with keys, this DEK is encrypted with the Key Encryption Key \(KEK\) of User 2 and stored in the Keystore in steps 40-42

