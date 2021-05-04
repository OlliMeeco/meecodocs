# Items and Slots

This guide describes creating Items and Item Templates using the Meeco API.


## Basic Terms

-   **Name** — A *machine-readable* non-empty string, for example `phone_number` or `postal_address`.
-   **Label** — A *human-readable* non-empty string, for example "Phone Number", or "Postal Address". Objects often have both a name and a label with a direct relationship between the two, as suggested by the example.
-   **Slot** — The smallest data entity in the vault. Each slot has a name, a label, and a value. Values can be strings, dates, or numbers, but also binaries like images or documents. Values are always encrypted. [Read more](terminology.md#slot)
-   **Item Template** — A list of empty slots with a label and a name. Each Item is created by cloning such a template and filling in the slots. More detail about [Item Templates in the Terminology section](terminology.md#an-item-template)
-   **Item** — Contains one or more slots with filled in values. Read more about [Items in the Terminology section](terminology.md#item)


## Browsing Item Templates

Items are created from templates, so we begin by listing all available Item templates:

```bash
curl --request GET 'https://sandbox.meeco.me/vault/item_templates' \
     -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
     -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY"
```

[API docs](<https://dev.meeco.me/api-details#api=meeco-vault-api&operation=get-item_templates-id>)

(Get `API_SUBSCRIPTION_KEY` by [signing up](<https://dev.meeco.me/signup>) for the API, then use the CLI tool to [generate a User and access token](<https://github.com/Meeco/js-sdk/tree/master/packages/cli>).)

The response JSON object lists Templates under the `item_templates` key. Each Template object has a `slot_ids` list, which references Slots in the top-level `slots` list.

Here is a truncated sample response:

```json
{
    "item_templates": [
      {
            "id": "ccfaadf6-e040-433f-ad34-904e988a2187",
            "name": "travel",
            "description": null,
            "ordinal": 10,
            "visible": true,
            "user_id": null,
            "updated_at": "2020-01-02T20:32:49.991Z",
            "template_type": "ItemTemplate",
            "classification_node_ids": [],
            "label": "Travel",
            "background_color": null,
            "image": "https://api-sandbox.meeco.me/images/7308dc39-d2b1-4039-9960-34f69dd06cd7",
            "association_ids": [],
            "associations_to_ids": [],
            "slot_ids": [
                "0cf77509-6eaf-49ff-a036-c3c7e2fee106",
                "f1668277-0db5-4cff-9210-08a2f245c4aa",
                "23eea243-1d09-4000-9215-7c1bc534c141",
                "b57a5d9a-f124-4ddd-954f-18681b2360f1",
                "929ea8b9-9815-4389-99a9-163f9e0c8b15"
            ]
        },
        ...
    ],
    "slots": [
        {
            "id": "f1668277-0db5-4cff-9210-08a2f245c4aa",
            "name": "return_date",
            "description": null,
            "encrypted": false,
            "ordinal": 2,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "ccfaadf6-e040-433f-ad34-904e988a2187",
            "slotable_type": "ItemTemplate",
            "required": false,
            "updated_at": "2020-01-02T20:32:49.761Z",
            "created_at": "2020-01-02T20:32:49.761Z",
            "config": null,
            "slot_type_name": "date",
            "creator": "system",
            "binary_ids": [],
            "label": "Return date",
            "image": null,
            "encrypted_value": null
        },
        ...
    ],
    "shares": [],
    "classification_nodes": [
        ...
    ],
    "associations": [],
    "associations_to": [],
    "attachments": [],
    "thumbnails": [],
    "meta": {
      "pages": null,
      "total_count": null
    }
}
```

Here's a sample of Item Templates you might get:

-   `passport_details`
-   `password`
-   `important_document`
-   `vehicle`
-   `travel`
-   `bank_item`
-   `membership_subscription`
-   `pet`
-   `device`
-   `important_document`
-   `custom`
-   `services`


### Finding a Specific Template

You can get a specific Template using its `id` (replace `ITEM-TEMPLATE-ID`):

```bash
curl --request GET 'https://sandbox.meeco.me/vault/item_templates/ITEM-TEMPLATE-ID' \
     -H 'Authorization: Bearer $VAULT_ACCESS_TOKEN' \
     -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY'
```

[API docs](<https://dev.meeco.me/api-details#api=meeco-vault-api&operation=get-item_templates-id>)

Or, to search Item Templates by matching `label` text (replace `SEARCH_TEXT`):

```bash
curl --request GET 'https://sandbox.meeco.me/vault/item_templates?like=SEARCH_TEXT' \
     -H 'Authorization: Bearer $VAULT_ACCESS_TOKEN' \
     -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY'
```

[API docs](<https://dev.meeco.me/api-details#api=meeco-vault-api&operation=get-item_templates-id>)


## Creating an Item

The example below creates an Item from the `vehicle` Template.

Using the CLI, we can see that the `vehicle` Template has the following Slots:

-   `model_make`
-   `licence_plate`
-   `vin`
-   `type`
-   `purchase_date`

For now the new Item's Slots are left empty. A [later section](#encryption-of-user-data) will cover encrypting data and adding it to a created Item.

To create an Item you must give the name of an existing Item Template, and a label:

```bash
  curl --request POST 'https://sandbox.meeco.me/vault/items' \
       -H 'Authorization: Bearer $VAULT_ACCESS_TOKEN' \
       -H 'content-type: application/json' \
       -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY' \
       --data \
'{
  "template_name": "vehicle",
  "item": {
    "label": "My Car"
  }
}'
```

[API docs](<https://dev.meeco.me/api-details#api=meeco-vault-api&operation=post-items>)

The API response is the newly created Item:

```json
{
    "item": {
        "id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
        "name": "vehicle",
        "label": "Vehicle",
        "description": null,
        "created_at": "2020-03-12T04:38:24.110Z",
        "item_template_id": "61516c6f-81df-4c86-96df-8af915f0aec0",
        "ordinal": 1,
        "visible": true,
        "updated_at": "2020-03-12T04:38:24.556Z",
        "item_template_label": "Vehicle",
        "shareable": false,
             ...
        "association_ids": [],
        "associations_to_ids": [],
        "slot_ids": [
            "f84d82be-7d82-4ccb-b7ae-e30be5b036c9",
            "6e1e4ecc-0b8b-45c1-919f-01e69f05bbf8",
            "f09975cc-ec8e-4744-b883-73c115d32434",
            "32d1f51b-d26e-4c85-8589-133a4f8e4579",
            "5addd943-948d-4f80-b1e8-771ff5fee2c1",
            "1fdd1473-a178-43dc-8862-c1aa242cf861",
            "e4d7f7ed-de2c-4819-9369-478f2357b6aa",
            "4cd09df4-9b69-4579-9e76-9a6d7c314b02"
        ]
    },
    "classification_nodes": [],
    "shares": [],
    "connections": [],
    "attachments": [],
    "thumbnails": [],
    "slots": [
        {
            "id": "f84d82be-7d82-4ccb-b7ae-e30be5b036c9",
            "name": "image",
            "description": null,
            "encrypted": false,
            "ordinal": 6,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.531Z",
            "created_at": "2020-03-12T04:38:24.531Z",
            "config": null,
            "slot_type_name": "image",
            "creator": "system",
            "binary_ids": [],
            "label": "Image",
            "image": null,
            "encrypted_value": null
        },
        ...

    ],
    "associations": [],
    "associations_to": []
}
```

Notice that Slots are created according to the Item Template, but are left empty for now.

Items can also be classified, that is described in [another page](classification-hierarchies.md).


### Item Names

An Item's `name` is auto-generated from its label. Names are all lower-case, have no non-alphanumeric characters, and have whitespace replaced with underscores. For example, label `A strange &8Label` would become `a_strange_8label`.

Any user specified names (for Slots and Items) are sanitized to this format.


Item names and labels do not have to be unique, unlike Item Template names.



### Extra Slots

The Slots in the Item Template are present in every Item created from that Template, but Items can have additional Slots too. Any extra Slots described in `slots_attributes` are created for the new Item.

For example, if `my_template` had Slots `foo` and `bar`, and we create an Item with

```json
{
  "template_name": "my_template",
  "item": {
    "label": "A Test Item",
    "slots_attributes": [
      {
        "label": "baz",
        "slot_type_name": "key_value"
      }
    ]
  }
}
```

Then the new Item will have Slots `foo`, `bar` and `baz`.

The next section has more information about creating Slots.


## Creating a Custom Template

It is possible to create a Custom Template which we can use to create our own Items.

Only the `label` property is required, it will auto-generate a `name` (as described above). Since Item Templates are referenced by their name, the generated name must be unique. You can specify it separately if the label does not generate a unique name.

```bash
    curl --request POST "https://sandbox.meeco.me/vault/item_templates" \
         -H "Content-Type: application/json" \
         -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
         -H "authorization: Bearer $VAULT_ACCESS_TOKEN" \
         --data \
 '{
  "name": "example_custom_template",
  "label": "Example Custom Template",
  "description": "An example template",
  "slots_attributes": [
    {
      "label": "An Example Slot",
      "slot_type_name": "key_value"
    }
  ]
}'
```

The new Template will look like this:

```json
{
  "item_template": {
    "id": "66c6b284-434e-4411-9435-7bd70a74c6d2",
    "name": "example_custom_template",
    "description": "An example template",
    "ordinal": 0,
    "visible": true,
    "user_id": "68a2cdb3-4a9d-42ac-83e7-d7e4967143a0",
    "updated_at": "2020-09-28T02:03:11.348Z",
    "label": "Example Custom Template",
    "slot_ids": [
      "e019d2a6-36ed-4768-9aec-273879273d23"
    ],
    "classification_node_ids": [],
    "background_color": null,
    "image": null
  },
  "slots": [
    {
      "id": "e019d2a6-36ed-4768-9aec-273879273d23",
      "name": "an_example_slot",
      "description": null,
      "encrypted": false,
      "ordinal": 0,
      "visible": true,
      "classification_node_ids": [],
      "item_id": null,
      "required": false,
      "updated_at": "2020-09-28T02:03:11.424Z",
      "created_at": "2020-09-28T02:03:11.376Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator": null,
      "label": "An Example Slot",
      "image": null,
      "attachment_id": null,
      "own": false,
      "share_id": null,
      "original_id": null,
      "owner_id": null,
      "encrypted_value": null,
      "encrypted_value_verification_key": null,
      "value_verification_hash": null
    }
  ],
  "classification_nodes": [],
  "attachments": [],
  "thumbnails": []
}
```

Then, you can create an item from your new template:

```bash
  curl --request POST "https://sandbox.meeco.me/vault/items" \
        -H "content-type: application/json" \
        -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
        -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
        --data \
'{
  "item": {
    "label": "An example item with custom template",
    "slots_attributes": []
  },
  "template_name": "example_custom_template"
}'
```

The created item comes back looking like the following, with a custom slot that was part of the template creation call.

```json
{
  "item": {
    "id": "98db3af3-c430-45ce-a5eb-cce89b19f736",
    "name": "an_example_item_with_custom_template",
    "label": "An example item with custom template",
    "description": "An example template",
    "created_at": "2020-09-28T02:12:56.194Z",
    "item_template_id": "66c6b284-434e-4411-9435-7bd70a74c6d2",
    "ordinal": 1,
    "visible": true,
    "updated_at": "2020-09-28T02:12:56.271Z",
    "item_template_label": "Example Custom Template",
    "item_image": null,
    "item_image_background_colour": null,
    "slot_image": null,
    "slot_image_background_colour": null,
    "category_image": null,
    "category_image_background_colour": null,
    "category_label": null,
    "original_id": null,
    "owner_id": "68a2cdb3-4a9d-42ac-83e7-d7e4967143a0",
    "share_id": null,
    "image": null,
    "image_background_colour": null,
    "me": false,
    "background_color": null,
    "classification_node_ids": [],
    "association_ids": [],
    "associations_to_ids": [],
    "slot_ids": [
      "445ee1d0-0100-458d-9f7a-4698b6aaf1f0"
    ],
    "own": true
  },
  "associations": [],
  "associations_to": [],
  "classification_nodes": [],
  "slots": [
    {
      "id": "445ee1d0-0100-458d-9f7a-4698b6aaf1f0",
      "name": "an_example_slot",
      "description": null,
      "encrypted": false,
      "ordinal": 0,
      "visible": true,
      "classification_node_ids": [],
      "item_id": "98db3af3-c430-45ce-a5eb-cce89b19f736",
      "required": false,
      "updated_at": "2020-09-28T02:12:56.247Z",
      "created_at": "2020-09-28T02:12:56.247Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator": "user",
      "label": "An Example Slot",
      "image": null,
      "attachment_id": null,
      "own": true,
      "share_id": null,
      "original_id": null,
      "owner_id": "68a2cdb3-4a9d-42ac-83e7-d7e4967143a0",
      "encrypted_value": null,
      "encrypted_value_verification_key": null,
      "value_verification_hash": null
    }
  ],
  "attachments": [],
  "thumbnails": []
}
```

Notice that the Item's `description` property inherits the `description` of the Item Template.

There are a few limitations on the use of Item Templates:

-   There currently isn't a way to share custom templates with other users.
-   Templates cannot be changed or deleted


### Slots

Slots represent a key-value pair, where the value is always encrypted. The Meeco Vault will check and reject any `encrypted_value` data that doesn't match the cryppo serialization format.

Slots are always owned by an Item (or an Item Template, but these Slots are never read), and news Slots are created for each new Item.

Their most important properties are:

| Property          | Description                 |
|----------------- |--------------------------- |
| `name`            | Machine-readable string     |
| `label`           | Display name                |
| `description`     | Longer name                 |
| `encrypted_value` | Output of Cryppo encryption |
| `slot_type_name`  | string                      |

The `slot_type_name` property must be one of

-   `key_value`
-   `bool`
-   `classification_node`
-   `color`
-   `date`
-   `datetime`
-   `image`
-   `note_text`
-   `select`
-   `attachment`
-   `url`
-   `phone_number`
-   `select_multiple`
-   `email`
-   `password`

As the Vault cannot inspect the data, it is just a suggestion to the user. Type `key_value` is the default.

Slots are created either by cloning an Item Template, or via the `slots_attributes` property when creating an Item. Since they are keyed by `name`, either `label` or `name` must be non-empty on creation.


Slots are updated by calling `PUT /vault/items` with the new data in `slots_attributes`:

```bash
curl --request PUT "https://sandbox.meeco.me/vault/items/bef961af-aa1f-4f1c-ac95-cdb41b3682db" \
     -H "content-type: application/json" \
     -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
     -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
     --data \
  '{
  "item": {
    "slots_attributes": [
      {
        "name": "bar",
        "label": "A new label"
      }
    ]
  }
}'
```

As Slots are keyed by their names, names should be unique per Item.

Slots can also be deleted by calling `DELETE /slot/id`. This deletes the Slot (and it's data) from the parent Item.

Slots in Item Templates cannot be deleted.


## Encryption of User Data

One of the core features of the Meeco platform is data encryption. User data stored in the Meeco Vault is encrypted in such a way that no one - including Meeco - can decrypt and read it other than the user.

If we want to store data in an Item we must encrypt it, otherwise the Vault will return an error.

To get familiar with the kinds of cryptographic key the Meeco platform uses please follow either the [Quickstart](../getting-started/quickstart.md) guide, or "[Setting Up Access to the Vault and Keystore](setting-up-access.md)". That will introduce you to the Cryppo library that we use to make encryption, decryption and serialization simpler in the context of the Meeco Service.

In the following example, we will use the Data Encryption Key (DEK) that the CLI generated for us from the Quickstart guide (and saved into the \`.user.yaml\` file) to encrypt a Slot value.

In the real world this process would involve a few more steps:

-   Reading the encrypted Key Encryption Key \(KEK\) from the Key Store
-   Decrypting it with the Password Derived Key \(PDK\)
-   Reading a DEK from the Key Store
-   Decrypting it with the KEK


If you do not have a DEK already, you can also generate one using the \`cryppo-cli\` and the following command:

```bash
cryppo genkey
```

Result:

```bash
URL-Safe Base64 encoded key:
3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
```

To encrypt slot value \`BMW\` run the following command:

```bash
cryppo encrypt -v BMW -k 3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
```

Result:

```bash
Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo=
```

The output above is the encrypted slot value ready to be stored in the Vault.

We can decrypt by running the following command:

```bash
cryppo decrypt -s Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo= -k 3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
BMW
```

The Meeco platform uses the serialization format of Cryppo. If no derived key is used, each such string contains three parts concatenated with a dot:

-   Encryption strategy name
-   Encoded encrypted data encoded with Base64
-   Encoded encryption artefacts serialized into a hash converted to YAML, then encoded with Base64

If you are feeling adventurous you are welcome to dig into the [Cryppo-CLI](<https://github.com/Meeco/cryppo-cli>)


The example below creates an Item config file. We need to provide a template name to create an item config file.

```bash
meeco items:create-config TEMPLATENAME > .item_config.yaml
```

This command will create the config file for the item to be created in the next step. The TEMPLATENAME can be any template from the list above.

The Meeco CLI can then create the item by the following command:

```bash
meeco items:create .item_config.yaml 
```

will create the Item encrypting the Slots described in `.item_config.yaml` using the current user's keys.

You can also integrate this flow into your app using the Meeco SDK's `UserService`.


### Filling Slot Values

Thanks to the Item Template our new Item already has a list of empty Slots, and a list of classification tags. Let's fill in a value of the \`encrypted<sub>value</sub>\` slot using the encrypted value from the previous step:

```bash
curl --request PUT \
 'https://sandbox.meeco.me/vault/items/049740cb-ad1f-43d9-9254-ae25eba30f47' \
  -H 'Authorization: $VAULT_ACCESS_TOKEN' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY' \
  -d '
    {
      "item": {
        "label": "vehicle",
        "slots_attributes": [
          {
            "label": "Make or model",
            "encrypted_value": "Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo=",
            "slot_type_name": "key_value",
            "name": "model_make"
          }
        ]
      }
    }
  '
```

Response:

```json
{
  "item": {
    ...
  },
  ...
  "slots" [
    ...
  {
    "id": "ce9d89f8-a50a-486a-b007-d1cb006ee157",
    "name": "model_make",
    "description": null,
    "encrypted": true,
    "ordinal": 1,
    "visible": true,
    "classification_node_ids": [],
    "slotable_id": "049740cb-ad1f-43d9-9254-ae25eba30f47",
    "slotable_type": "Item",
    "required": false,
    "updated_at": "2020-03-18T06:49:31.160Z",
    "created_at": "2020-03-18T06:49:17.549Z",
    "config": null,
    "slot_type_name": "key_value",
    "creator": "system",
    "binary_ids": [],
    "label": "Make or model",
    "image": null,
    "encrypted_value": "Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo="
  },
  ],
}
```


## Shared Items

The page on [Connections and Sharing](connections-and-sharing.md) covers sharing Items. This section will just describe some properties of shared Items.


### Receiving A Share

You receive a shared item by calling `PUT https://sandbox.meeco.me/vault/incoming_shares/{share_id}/accept`. (This indicates you accept the share terms, if any). Next call `GET https://sandbox.meeco.me/vault/incoming_shares/{share_id}/item` and view the Item that has been created in your Vault. Note that `share.item_id` is the <span class="underline">original</span> Item's id, not the one in your Vault!


### Owners

An Item's owner is its original creator. The following table summarizes the how properties of an Item change when you are the owner, vs the receiver of a shared copy.

| Property      | Owner Vaule | Receiver Value  |
|------------- |----------- |--------------- |
| `own`         | true        | false           |
| `original_id` | null        | `share.item_id` |
| `share_id`    | null        | `share.id`      |

As a result, if `item.share_id` is non-null, then it is a share you received, and you can view the share via `GET https://sandbox.meeco.me/vault/incoming_shares/{item.share_id}`.

Owners have the ability to push updates of shared data, and can share the Item with anyone. Receivers of a shared Item can only share that Item if `item.sharing_mode` is `anyone`.
