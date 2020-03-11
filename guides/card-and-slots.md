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
  --url https://meeco-api-dev.azure-api.net/vault/item_templates \
  -H 'authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY'
```

The response is pretty large, and it lists all available stories and their slots. Slots are given in a separate list and can be matched by their IDs:

```javascript
{
    "story_templates": [],
    "slots": [],
    ...
}
```

Besides these 2 lists there are a few other lists which we will ignore for the time being.

Here is a truncated sample response:

```javascript
{
    "story_templates": [
      {
        "id": "ccfaadf6-e040-433f-ad34-904e988a2187",
        "name": "travel",
        "description": null,
        "ordinal": 10,
        "visible": true,
        "user_id": null,
        "updated_at": "2020-01-02T20:32:49.991Z",
        "template_type": "StoryTemplate",
        "classification_node_ids": [
          "29bc3762-802b-42d7-a129-c10fa1e43227",
          "7f7b94f6-5a25-4de1-a2d9-2c0822aa69aa"
        ],
        "label": "Travel",
        "image": "https://meeco-api-dev.azure-api.net/vault/global/images/7308dc39-d2b1-4039-9960-34f69dd06cd7",
        "association_ids": [],
        "associations_to_ids": [],
        "slot_ids": [
          "f1668277-0db5-4cff-9210-08a2f245c4aa",
          "0cf77509-6eaf-49ff-a036-c3c7e2fee106",
          "23eea243-1d09-4000-9215-7c1bc534c141",
          "b57a5d9a-f124-4ddd-954f-18681b2360f1",
          "929ea8b9-9815-4389-99a9-163f9e0c8b15"
        ],
        "background_color": null
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
        "slotable_type": "TileTemplate",
        "required": false,
        "updated_at": "2020-01-02T20:32:49.761Z",
        "created_at": "2020-01-02T20:32:49.761Z",
        "config": null,
        "slot_type_name": "date",
        "creator_type_name": "system",
        "binary_ids": [],
        "value": "",
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
    --url 'https://meeco-api-dev.azure-api.net/vault/item_templates?by_exact_name=vehicle' \
    -H 'accept: application/json' \
    -H 'authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
    -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY'
```

or `story_template` `id` \(`$['story_template']['id']`\):

```bash
curl --request GET \
  --url https://meeco-api-dev.azure-api.net/vault/item_templates/STORY-TEMPLATE-ID \
  -H 'authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY'
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
  --url https://meeco-api-dev.azure-api.net/vault/items \
  -H 'accept: application/json' \
  -H 'authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  --data '{"template_name": "vehicle"}'
```

Here is a response with a created card:

```javascript
{
  "item": {
    "id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
    "name": "vehicle",
    "label": "Vehicle",
    "description": null,
    "created_at": "2020-02-03T08:33:41.713Z",
    "tile_template_id": "61516c6f-81df-4c86-96df-8af915f0aec0",
    "ordinal": 1,
    "visible": true,
    "updated_at": "2020-02-03T08:33:42.297Z",
    "tile_template_label": "Vehicle",
    "shareable": false,
    "image": "https://meeco-api-dev.azure-api.net/vault/global/images/ef8ef92e-e4be-4f50-bfce-774a608098ca",
    "image_background_colour": null,
    "tile_image": null,
    "tile_image_background_colour": null,
    "slot_image": null,
    "slot_image_background_colour": null,
    "activity_image": null,
    "activity_image_background_colour": null,
    "category_image": null,
    "category_image_background_colour": null,
    "classification_node_ids": [
      "7f7b94f6-5a25-4de1-a2d9-2c0822aa69aa",
      "54ce2d77-764a-41e5-9f79-d313e07554b6"
    ],
    "category_label": null,
    "type": "StoryItem",
    "association_ids": [],
    "associations_to_ids": [],
    "slot_ids": [
      "8f667106-de6f-433b-a7eb-ee40fbf6d376",
      "e49a909f-dbbe-4dc0-8415-74fe2016a602",
      "9120be11-4a43-4328-b9a3-aa0e58111ee6",
      "2072e277-8c57-455a-9468-50970d04199e",
      "587aa5ec-e8f9-4186-98b5-dc014cfc1e65",
      "6872bdc3-3461-47e9-9418-f940bf329283",
      "8d4a5202-5787-4d1a-9cfc-281bb2cd2eb6",
      "a1c2c6f5-a07a-4294-9547-3c37fb74025d"
    ],
    "me": false,
    "background_color": null
  },
  "classification_nodes": [
    {
      "id": "7f7b94f6-5a25-4de1-a2d9-2c0822aa69aa",
      "background_color": null,
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
      "label": "eSafe Templates",
      "name": "esafe_templates",
      "ordinal": 1,
      "scheme": "esafe"
    },
    {
      "id": "54ce2d77-764a-41e5-9f79-d313e07554b6",
      "background_color": "#FF00FF",
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/ef8ef92e-e4be-4f50-bfce-774a608098ca",
      "label": "Vehicles",
      "name": "vehicles",
      "ordinal": 3,
      "scheme": "esafe"
    }
  ],
  "shares": [],
  "connections": [],
  "attachments": [],
  "thumbnails": [],
  "slots": [
    {
      "id": "8f667106-de6f-433b-a7eb-ee40fbf6d376",
      "name": "image",
      "description": null,
      "encrypted": false,
      "ordinal": 6,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:42.211Z",
      "created_at": "2020-02-03T08:33:42.211Z",
      "config": null,
      "slot_type_name": "image",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Image",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "e49a909f-dbbe-4dc0-8415-74fe2016a602",
      "name": "tags",
      "description": null,
      "encrypted": false,
      "ordinal": 4,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:42.157Z",
      "created_at": "2020-02-03T08:33:42.157Z",
      "config": {
        "classification_scheme_name": "tag",
        "selection_type": "multiple",
        "can_create": true
      },
      "slot_type_name": "classification_node",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Tags",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "9120be11-4a43-4328-b9a3-aa0e58111ee6",
      "name": "story_category",
      "description": null,
      "encrypted": false,
      "ordinal": 3,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:42.098Z",
      "created_at": "2020-02-03T08:33:42.098Z",
      "config": {
        "classification_scheme_name": "story_category",
        "selection_type": "single",
        "editable": true
      },
      "slot_type_name": "classification_node",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Category",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "2072e277-8c57-455a-9468-50970d04199e",
      "name": "purchase_date",
      "description": null,
      "encrypted": false,
      "ordinal": 5,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:42.001Z",
      "created_at": "2020-02-03T08:33:42.001Z",
      "config": null,
      "slot_type_name": "date",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Date purchased",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "587aa5ec-e8f9-4186-98b5-dc014cfc1e65",
      "name": "type",
      "description": null,
      "encrypted": false,
      "ordinal": 4,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:41.954Z",
      "created_at": "2020-02-03T08:33:41.954Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Type",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "6872bdc3-3461-47e9-9418-f940bf329283",
      "name": "vin",
      "description": null,
      "encrypted": false,
      "ordinal": 3,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:41.888Z",
      "created_at": "2020-02-03T08:33:41.888Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "VIN",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "8d4a5202-5787-4d1a-9cfc-281bb2cd2eb6",
      "name": "licence_plate",
      "description": null,
      "encrypted": false,
      "ordinal": 2,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:41.837Z",
      "created_at": "2020-02-03T08:33:41.837Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Vehicle registration number",
      "image": null,
      "encrypted_value": null
    },
    {
      "id": "a1c2c6f5-a07a-4294-9547-3c37fb74025d",
      "name": "model_make",
      "description": null,
      "encrypted": false,
      "ordinal": 1,
      "visible": true,
      "classification_node_ids": [],
      "slotable_id": "9d2c2a14-feec-4320-bb71-5b5761b9f085",
      "slotable_type": "TileItem",
      "required": false,
      "updated_at": "2020-02-03T08:33:41.771Z",
      "created_at": "2020-02-03T08:33:41.771Z",
      "config": null,
      "slot_type_name": "key_value",
      "creator_type_name": "system",
      "binary_ids": [],
      "value": "",
      "label": "Make or model",
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

To generate a DEK, change to the `:/meeco-documentation/client_scripts/guide03` directory and run the following command:

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
 'https://meeco-api-dev.azure-api.net/vault/items/0f56c2ff-acfb-4838-80b3-b9d41ff368f5' \
  -H 'Authorization: DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
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

