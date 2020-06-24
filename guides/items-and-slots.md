# Items and Slots

#### Basic Terms

* **Items** — An _Item_ is the basic container for all your encrypted data. Data can be strings, dates, numbers, but also binaries like images or documents. Read more about [Items in the Terminology section](terminology.md#item)
* **Slot** — A _slot_ is the smallest data entity in the vault. A slot is a placeholder for a data value. An Item usually contains a number of slots. Each slot has a name, a label, and a value. Read more about [slots in the Terminology section](terminology.md#slot)
* **Item Template** — is a predefined list of empty slots with a label and a name. Each Item is created by cloning such a template and filling in the slots. More detail about [Item Templates in the Terminology section](terminology.md#an-item-template)

## Browsing Item Templates

Because creating an Item begins with an Item template, we can begin with listing all available Item templates:

```bash
    curl --request GET \
  --url https://sandbox.meeco.me/vault/item_templates \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

The response returns a lot of data, and it lists all available items and their slots. Slots are given in a separate list and can be matched by their IDs:

The response is pretty large, and it lists all available Items and their slots. Slots are given in a separate list and can be matched by their IDs:

```javascript
{
    "item_templates": [],
    "slots": [],
    ...
}
```

Besides these 2 lists there are a few other lists which we will ignore for the time being.

Here is a truncated sample response:

```javascript
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

Here's a sample list of Item templates you might get:

* `passport_details`
* `password`
* `important_document`
* `vehicle`
* `travel`
* `bank_item`
* `membership_subscription`
* `pet`
* `device`
* `important_document`
* `custom`
* `services`

We can also search a certain Item template by its `name`:

```bash
curl --request GET \
    --url 'https://sandbox.meeco.me/vault/item_templates?by_exact_name=vehicle' \
    -H 'accept: application/json' \
    -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
    -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

or `item_templates` `id` \(`$['item_templates']['id']`\):

```bash
curl --request GET \
  --url 'https://sandbox.meeco.me/vault/item_templates/ITEM-TEMPLATE-ID' \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

Here is a sample list of slots and their types for template "Vehicle":

* Make or Model — `key_value`
* VIN — `key_value`
* Vehicle Registration Number — `key_value`
* Date Purchased — `date`
* Image — `image`

## Creating an Item

Now it is about time to create an actual Item based on an Item template. In this example, we will only specify the name of the template and define no slots:

```bash
curl --request POST \
  --url 'https://sandbox.meeco.me/vault/items' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  --data '{"template_name": "vehicle"}'
```

Here is a response with a created Item:

```javascript
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
## Creating a Custom Template and Item

It is possible to create a Custom Template, and then call on this template later on.

The minimum amount of information is quite small, but you can add as many slots as you like. Here, we've only added one slot and given the Template and Item a name.

```bash
curl -v -X POST "https://sandbox.meeco.me/vault/item_templates"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: a39e5fb966974c29a03930e8bb473198"
-H "Authorization: Z1yHMPz6yAXF79QZedP6"
--data-ascii "{
  ^"name^": ^"example_custom_template^",
  ^"label^": ^"Example Custom Template^",
  ^"slots_attributes^": [
    {
      ^"label^": ^"An Example Slot^",
      ^"slot_type_name^": ^"key_value^"
    }
  ]
}"
```
The the successfully created template will look like this:

```JSON
{
    "item_template": {
        "id": "8326475a-65c2-4752-aba0-80396387612a",
        "name": "example_custom_template",
        "description": null,
        "ordinal": 0,
        "visible": true,
        "user_id": "1788125e-3477-4633-8797-bebbf3b6e359",
        "updated_at": "2020-06-24T06:31:09.549Z",
        "template_type": "ItemTemplate",
        "label": "Example Custom Template",
        "slot_ids": ["2f007037-01d9-495f-9999-2a4cc259eac9", "23eea243-1d09-4000-9215-7c1bc534c141", "b57a5d9a-f124-4ddd-954f-18681b2360f1", "929ea8b9-9815-4389-99a9-163f9e0c8b15"],
        "classification_node_ids": [],
        "background_color": null,
        "image": null
    },
    "slots": [{
        "id": "2f007037-01d9-495f-9999-2a4cc259eac9",
        "name": "an_example_slot",
        "description": null,
        "encrypted": false,
        "ordinal": 0,
        "visible": true,
        "classification_node_ids": [],
        "slotable_id": null,
        "slotable_type": null,
        "required": false,
        "updated_at": "2020-06-24T06:31:09.528Z",
        "created_at": "2020-06-24T06:31:09.498Z",
        "config": null,
        "slot_type_name": "key_value",
        "creator": null,
        "attachment_ids": [],
        "label": "An Example Slot",
        "image": null,
        "encrypted_value": null
    }, { ... }, { ... }, { ... }],
    "classification_nodes": [],
    "attachments": [],
    "thumbnails": []
}
```

Then, you will create an item from your new template:

```bash
curl -v -X POST "https://sandbox.meeco.me/vault/items"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: a39e5fb966974c29a03930e8bb473198"
-H "Authorization: Z1yHMPz6yAXF79QZedP6"
--data-ascii "  {
    ^"item^": {
      ^"label^": ^"An example item with custom template^",
      ^"classification_nodes_attributes^": [
      ],
      ^"slots_attributes^": [
      ]
    },
    ^"template_name^": ^"example_custom_template^"
  }"
```

The created item comes back looking like the following (truncated) response, with a custom slot that was part of the template creation call.

```JSON
{
    "item": {
        "id": "945f5a3f-ea60-4556-b22c-7127332be319",
        "name": "an_example_item_with_custom_template",
        "label": "An example item with custom template",
        "description": null,
        "created_at": "2020-06-24T06:39:26.102Z",
        "item_template_id": "8326475a-65c2-4752-aba0-80396387612a",
        "ordinal": 1,
        "visible": true,
        "updated_at": "2020-06-24T06:39:26.457Z",
        "item_template_label": "Example Custom Template",
        "shareable": false,
        "item_image": null,
        "item_image_background_colour": null,
        "slot_image": null,
        "slot_image_background_colour": null,
        "category_image": null,
        "category_image_background_colour": null,
        "category_label": null,
        "image": null,
        "image_background_colour": null,
        "me": false,
        "background_color": null,
        "classification_node_ids": [],
        "association_ids": [],
        "associations_to_ids": [],
        "slot_ids": ["7d565864-ea20-4029-b2fa-ce7d8041e883", "1dd85852-da16-4e37-acff-d598419c7f05", "5dd8ef08-c598-45b6-97f0-c2b8d98c8df0", "e142910d-ac0c-4b01-b99b-58aab60e63b9"]
    },
    "associations": [],
    "associations_to": [],
    "classification_nodes": [],
    "slots": [{ ... },
    {
        "id": "e142910d-ac0c-4b01-b99b-58aab60e63b9",
        "name": "an_example_slot",
        "description": null,
        "encrypted": false,
        "ordinal": 0,
        "visible": true,
        "classification_node_ids": [],
        "slotable_id": "945f5a3f-ea60-4556-b22c-7127332be319",
        "slotable_type": "Item",
        "required": false,
        "updated_at": "2020-06-24T06:39:26.253Z",
        "created_at": "2020-06-24T06:39:26.253Z",
        "config": null,
        "slot_type_name": "key_value",
        "creator": "user",
        "attachment_ids": [],
        "label": "An Example Slot",
        "image": null,
        "encrypted_value": null
    }],
    "attachments": [],
    "thumbnails": [],
    "shares": []
}
```

_Note - There currently isn't a way to share custom templates with other users._

## Data Encryption Of User Data In The Vault

One of the core features of the Meeco platform is data encryption. User data stored in the Meeco Vault is encrypted in such a way that no one - including Meeco - can decrypt and read it other than the user.

If we want to store data in an Item, we have to encrypt it, or the Vault will refuse to store unencrypted data and return an error.

To get familiar with the kinds of cryptographic key the Meeco platform uses please follow either the [Quickstart](../getting-started/quickstart.md) guide, or "[Setting Up Access to the Vault and Keystore](setting-up-access.md)". That will introduce you to the Cryppo library that we use to make encryption, decryption and serialization simpler in the context of the Meeco Service.

In the following example, we will use the Data Encryption Key \(DEK\) that the CLI generated for us from the Quickstart guide and saved into the `.user.yaml` file. We will use this to encrypt a slot value. Of course, in the real world scenario this process would involve a few more steps:

* Reading the encrypted Key Encryption Key \(KEK\) from the Key Store
* Decrypting it with the Password Derived Key \(PDK\)
* Reading a DEK from the Key Store
* Decrypting it with the KEK

If you followed the Meeco CLI guide, you will have already created a user with a DEK which you can find in the `.user.yaml` file. You can also generate a DEK by using the `cryppo-cli` and using the following command: 

```bash
cryppo genkey
```

Result:

```bash
URL-Safe Base64 encoded key:
3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
```

To encrypt slot value `BMW` run the following command:

```bash
cryppo encrypt -v BMW -k 3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
```

Result:

```bash
Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo=
```

The output above is the encrypted slot value ready to be stored in the Vault.

We can also test decryption by running the following command:

```bash
cryppo decrypt -s Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo= -k 3YazDa71zVVCzh_6JRd_M-J5bOKUS5HtFGLNR45prPg=
BMW
```

The Meeco platform uses the serialization format of Cryppo. If no derived key is used, each such string contains 3 parts concatenated with a dot:

* Encryption strategy name
* Encoded encrypted data encoded with Base64
* Encoded encryption artefacts serialized into a hash converted to YAML, then encoded with Base64

Cryppo-CLI has commands which do the same tasks:

* generate a key
* encrypt with a key
* decrypt with a key

We do this using OpenSSL directly, plus some standard Unix utilities.

If you are feeling adventurous you are welcome to dig into the [Cryppo-CLI](https://github.com/Meeco/cryppo-cli)

## Filling in a slot

Thanks to the template our newly created Item already has a list of empty fields \(slots\), and a list of classification tags.

Let's fill in a value of the `encrypted_value` slot using the encrypted value from the previous step:

```bash
curl -i -X PUT \
 'https://sandbox.meeco.me/vault/items/049740cb-ad1f-43d9-9254-ae25eba30f47' \
  -H 'Authorization: VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  -d '
    {
      "item": {
        "classification_nodes_attributes": [],
        "label": "vehicle",
        "slots_attributes": [
          {
            "label": "Make or model",
            "encrypted_value": "Aes256Gcm.MtK4.LS0tCml2OiAhYmluYXJ5IHwtCiAgdXN4NnozRnRUc2FldlBmdgphdDogIWJpbmFyeSB8LQogIHc2YmRTM252Z2o4TTZYSE9FUnhHd1E9PQphZDogbm9uZQo=",
            "slot_type_name": "key_value",
            "image": null,
            "config": null,
            "name": "model_make",
            "_destroy": false
          }
        ]
      }
    }
  '
```

Response:

```javascript
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

