# Card and Slots

## Basic Concepts

* **Card** — A _card_ is the basic container for all your encrypted data. Data can be strings, dates, numbers, but also binaries like images or documents. You can have as many cards as you want and these are what you typically want to keep safe in your personal data vault and selectively share information from. A card can be to store related data, for example, user profile, a club membership, and so on. Some endpoints refer to slots as _stories_. Please Consider _story_ a synonym
* **Slot** — A _slot_ is the smallest data entity in the vault. A slot is a placeholder for a data value. A _card_ usually contains a number of slots. Each slot has a name, a label, and a value. Slots have types. A slot type defines what can be stored in a slot and how this data is handled. Supported slot types include:
  * bool
  * classification\_node
  * color
  * date
  * datetime
  * image
  * key\_value
  * note\_text
  * select
  * attachment
  * url
  * phone\_number
  * select\_multiple
  * email
  * password
* **Card Template** — is a predefined list of empty slots with a label and a name. Each card is created by cloning such a template and filling in the slots.

## User must sign in first

## Browsing Card Templates

Because creating a card begins with a card template, we can begin with listing all available card templates:

```bash
    curl --request GET \
  --url https://sandbox.meeco.me/vault/item_templates \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

The response is pretty large, and it lists all available items and their slots. Slots are given in a separate list and can be matched by their IDs:

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
        ....
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

Here's a sample list of card templates you might get:

* passport\_details
* password
* important\_document
* vehicle
* travel
* bank\_card
* membership\_subscription
* pet
* device
* important\_document
* custom
* services

We can also search a certain card template by its `name`:

```bash
curl --request GET \
    --url 'https://sandbox.meeco.me/vault/item_templates?by_exact_name=vehicle' \
    -H 'accept: application/json' \
    -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
    -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

or `story_template` `id` \(`$['story_template']['id']`\):

```bash
curl --request GET \
  --url https://meeco-api-dev.azure-api.net/vault/item_templates/STORY-TEMPLATE-ID \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY'
```

Here is a sample list of slots and their types for template "Vehicle":

* Make or Model — `key_value`
* VIN — `key_value`
* Vehicle Registration Number — `key_value`
* Date Purchased — `date`
* Image — `image`

## Creating a Card

Now it is about time to create an actual card based on a card template. In this example, we will only specify the name of the template and define no slots:

```bash
curl --request POST \
  --url https://sandbox.meeco.me/vault/items \
  -H 'accept: application/json' \
  -H 'authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  --data '{"template_name": "vehicle"}'
```

Here is a response with a created card:

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
        "me": false,
        "background_color": null,
        "image": "https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca",
        "image_background_colour": null,
        "item_image": "https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca",
        "item_image_background_colour": null,
        "slot_image": null,
        "slot_image_background_colour": null,
        "activity_image": "https://api-sandbox.meeco.me/images/ef8ef92e-e4be-4f50-bfce-774a608098ca",
        "activity_image_background_colour": null,
        "category_image": null,
        "category_image_background_colour": null,
        "classification_node_ids": [],
        "category_label": null,
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
        {
            "id": "6e1e4ecc-0b8b-45c1-919f-01e69f05bbf8",
            "name": "tags",
            "description": null,
            "encrypted": false,
            "ordinal": 4,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.494Z",
            "created_at": "2020-03-12T04:38:24.494Z",
            "config": {
                "classification_scheme_name": "tag",
                "selection_type": "multiple",
                "can_create": true
            },
            "slot_type_name": "classification_node",
            "creator": "system",
            "binary_ids": [],
            "label": "Tags",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "f09975cc-ec8e-4744-b883-73c115d32434",
            "name": "story_category",
            "description": null,
            "encrypted": false,
            "ordinal": 3,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.441Z",
            "created_at": "2020-03-12T04:38:24.441Z",
            "config": {
                "classification_scheme_name": "story_category",
                "selection_type": "single",
                "editable": true
            },
            "slot_type_name": "classification_node",
            "creator": "system",
            "binary_ids": [],
            "label": "Category",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "32d1f51b-d26e-4c85-8589-133a4f8e4579",
            "name": "type",
            "description": null,
            "encrypted": false,
            "ordinal": 4,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.364Z",
            "created_at": "2020-03-12T04:38:24.364Z",
            "config": null,
            "slot_type_name": "key_value",
            "creator": "system",
            "binary_ids": [],
            "label": "Type",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "5addd943-948d-4f80-b1e8-771ff5fee2c1",
            "name": "vin",
            "description": null,
            "encrypted": false,
            "ordinal": 3,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.326Z",
            "created_at": "2020-03-12T04:38:24.326Z",
            "config": null,
            "slot_type_name": "key_value",
            "creator": "system",
            "binary_ids": [],
            "label": "VIN",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "1fdd1473-a178-43dc-8862-c1aa242cf861",
            "name": "model_make",
            "description": null,
            "encrypted": false,
            "ordinal": 1,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.284Z",
            "created_at": "2020-03-12T04:38:24.284Z",
            "config": null,
            "slot_type_name": "key_value",
            "creator": "system",
            "binary_ids": [],
            "label": "Make or model",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "e4d7f7ed-de2c-4819-9369-478f2357b6aa",
            "name": "licence_plate",
            "description": null,
            "encrypted": false,
            "ordinal": 2,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.239Z",
            "created_at": "2020-03-12T04:38:24.239Z",
            "config": null,
            "slot_type_name": "key_value",
            "creator": "system",
            "binary_ids": [],
            "label": "Vehicle registration number",
            "image": null,
            "encrypted_value": null
        },
        {
            "id": "4cd09df4-9b69-4579-9e76-9a6d7c314b02",
            "name": "purchase_date",
            "description": null,
            "encrypted": false,
            "ordinal": 5,
            "visible": true,
            "classification_node_ids": [],
            "slotable_id": "e053853d-6a7e-476b-8e3b-d78b4e6d2802",
            "slotable_type": "Item",
            "required": false,
            "updated_at": "2020-03-12T04:38:24.191Z",
            "created_at": "2020-03-12T04:38:24.191Z",
            "config": null,
            "slot_type_name": "date",
            "creator": "system",
            "binary_ids": [],
            "label": "Date purchased",
            "image": null,
            "encrypted_value": null
        }
    ],
    "associations": [],
    "associations_to": []
}
```

## Data Encryption Of User Data In The Vault

One of the core features of the Meeco platform is data encryption. User data stored in the Meeco Vault is encrypted in such a way that no one - including Meeco - can decrypt and read it other than the user.

If we want to store data in a card, we have to encrypt it, or the Vault will refuse to store unencrypted data and return an error.

To get familiar with the kinds of cryptographic key the Meeco platform uses please follow the "Setting Up Access to the Vault and Keystore". That will introduce you to the Cryppo library that we use to make encryption, decryption and serialization simpler in the context of the Meeco Service.

In the following example, we will generate a Data Encryption Key \(DEK\) and use it to encrypt a slot value. Of course, in the real world scenario this process would involve a few more steps:

* Reading the encrypted Key Encryption Key \(KEK\) from the Key Store
* Decrypting it with the Password Derived Key \(PDK\)
* Reading a DEK from the Key Store
* Decrypting it with the KEK

If you followed the Meeco CLI guide, you will have already created a user with a DEK which you can find in the `.user.yaml` file.

```bash
./00.make_key.rb
```

Result:

```bash
unencrypted data encryption key:
"PpyPTEmLwxYtmRrTk/Gp5qMf1eioSJOzQ8aOUCDqOks=\n"
```

The code for generation of the key is as follows:

```ruby
encryption_strategy = Cryppo::EncryptionStrategies::Aes256Gcm.new
data_encryption_key = encryption_strategy.generate_key
```

To encrypt slot value `BMW` run the following command:

```bash
./01.encrypt_data.rb "PpyPTEmLwxYtmRrTk/Gp5qMf1eioSJOzQ8aOUCDqOks=\n" BMW
```

Result:

```bash
serialized encrypted data:
"Aes256Gcm.hS6G.LS0tCml2OiAhYmluYXJ5IHwtCiAgaXJvKzRzVVg1dEVXR3J2cAphdDogIWJpbmFyeSB8LQogIFhjYk9KOXYwU0lFc1VKc2ZpeVJncFE9PQphZDogbm9uZQo="
```

`Aes256Gcm.hS6G.LS0tCml2OiAhYmluYXJ5IHwtCiAgaXJvKzRzVVg1dEVXR3J2cAphdDogIWJpbmFyeSB8LQogIFhjYk9KOXYwU0lFc1VKc2ZpeVJncFE9PQphZDogbm9uZQo=` is the slot value ready to be stored in the Vault.

The code for the encryption of key is as follows:

```ruby
encryption_strategy = Cryppo::EncryptionStrategies::Aes256Gcm.new
encrypted = Cryppo.encrypt(encryption_strategy.strategy_name, dek, data_to_encrypt)
serialized_encrypted = encrypted.serialize
```

We can also test decryption by running the following command:

```bash
./02.decrypt_data.rb "PpyPTEmLwxYtmRrTk/Gp5qMf1eioSJOzQ8aOUCDqOks=\n" "Aes256Gcm.hS6G.LS0tCml2OiAhYmluYXJ5IHwtCiAgaXJvKzRzVVg1dEVXR3J2cAphdDogIWJpbmFyeSB8LQogIFhjYk9KOXYwU0lFc1VKc2ZpeVJncFE9PQphZDogbm9uZQo="
BMW
```

The Meeco platform uses the serializaion format of Cryppo. If no derived key is used, each such string contains 3 parts concatenated with a dot:

* Encryption strategy name
* Encoded encrypted data encoded with Base64
* Encoded encryption artefacts serialized into a hash converted to YAML, then encoded with Base64

The directory wth scripts contains shell scripts which do the same tasks:

* generate a key
* encrypt with a key
* decrypt with a key

We do this using OpenSSL directly, plus some standard Unix utilities.

If you are feeling adventurous you are welcome to read these scripts.

## Filling in a slot

Thanks to the template our newly created card already has a list of empty fields \(slots\), and a list of classification tags.

Let's fill in a value of the `encrypted_value` slot using the encrypted value from the previous step:

```bash
curl -i -X PUT \
 'https://sandbox.meeco.me/vault/items/0f56c2ff-acfb-4838-80b3-b9d41ff368f5' \
  -H 'Authorization: VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  -d '
    {
      "story_item": {
        "classification_nodes_attributes": [],
        "label": "pet",
        "slots_attributes": [
          {
            "label": "Breed",
            "encrypted_value": "Aes256Gcm.2hDl.LS0tCml2OiAhYmluYXJ5IHwtCiAgQWQwSThDZk5qRnFycmFuMAphdDogIWJpbmFyeSB8LQogIDJXVklzbUxOSWVoOHZIVDB1ZzBtZVE9PQphZDogbm9uQQo=",
            "slot_type_name": "key_value",
            "image": null,
            "config": null,
            "name": "breed_species",
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
          "id": "06e53c16-d34c-48ee-bfc1-78ad93c9886f",
          "name": "breed_species",
          "description": null,
          "encrypted": true,
          "ordinal": 3,
          "visible": true,
          "classification_node_ids": [],
          "slotable_id": "0f56c2ff-acfb-4838-80b3-b9d41ff368f5",
          "slotable_type": "TileItem",
          "required": false,
          "updated_at": "2020-02-06T04:20:29.553Z",
          "created_at": "2020-02-06T02:02:38.623Z",
          "config": null,
          "slot_type_name": "key_value",
          "creator_type_name": "system",
          "binary_ids": [],
          "value": "",
          "label": "Breed",
          "image": null,
          "encrypted_value": "Aes256Gcm.2hDl.LS0tCml2OiAhYmluYXJ5IHwtCiAgQWQwSThDZk5qRnFycmFuMAphdDogIWJpbmFyeSB8LQogIDJXVklzbUxOSWVoOHZIVDB1ZzBtZVE9PQphZDogbm9uQQo="
        },
      ],
    }
```

