# Classification Hierarchies

In the Meeco Vault, Items, Item Templates and Slots can be tagged with ****Classification Nodes****. If an Item has been tagged with a Classification Node, you can find it again by searching for that Classification Node.

Classification Nodes are a lot like tags, but are grouped into Schemes. Schemes might represent similar topics, or classifications from an existing app.

A Classification Node is structured like

| Property    | Type   | Description                                 |
|-------------|--------|---------------------------------------------|
| name        | string | Machine-readable name                       |
| label       | string | Human-readable name                         |
| description | string | Explains what the classification represents |
| scheme      | string | See [Schemes](#schemes)                     |

## Browsing Classification Nodes

The `$VAULT_ACCESS_TOKEN` can be grabbed from the user file you created in the [Quickstart](../getting-started/quickstart.md) guide.

All classification nodes can be queried by `GET /vault/classification_nodes`:

```bash
curl --request GET \
  'https://sandbox.meeco.me/vault/classification_nodes' \
  -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
  -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY"
```

[API Docs](https://dev.meeco.me/api-details#api=meeco-vault-api&operation=get-classification_nodes)

You can modify the request with the following query parameters:

-   `scheme_name`, see below
-   `by_name` - a "LIKE" search which will return results for partial matches - i.e. 'fin' will return 'financial'


## Schemes

Classification Nodes are grouped by Schemes. Available Schemes are set by the Vault and cannot be changed.

These are the existing schemes:

-   `tag`
-   `country`
-   `meeco_wallet`
-   `region`

You must use `tag` as the default scheme.


## Creating a Classification Node

You can create a Classification node as follows

```bash
  curl --request POST 'https://sandbox.meeco.me/vault/classification_nodes' \
       -H "authorization: Bearer $VAULT_ACCESS_TOKEN" \
       -H 'content-type: application/json' \
       -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY' \
       --data \
'{
  "classification_node": {
    "classification_scheme_name": "tag",
    "name": "my-new-tag",
    "label": "My Tag",
    "description": "Hi There this is my Tag!"
  }
}'
```

[API Docs](https://dev.meeco.me/api-details#api=meeco-vault-api&operation=post-classification_nodes)

```json
{
  "classification_node": {
    "id": "b4ff857f-6f50-4608-bd93-b61a7dd012d5",
    "background_color": null,
    "description": "Hi There this is my Tag!",
    "image": null,
    "label": "My Tag",
    "name": "my-new-tag",
    "ordinal": 0,
    "scheme": "tag"
  }
}
```

Properties `classification_scheme_name` and either `name` or `label` are mandatory. As mentioned above, `classification_scheme_name` should usually be 'tag'. As for other Vault objects, if only `label` is given, then the name is created by translating the label text.

Since `name` is used to link a Classification Node to a Vault object, it must be unique.

Note that currently, as for Item Templates, the API does not allow:

-   updating Classification Nodes,
-   deleting Classification Nodes,
-   sharing new Classification Nodes


## Applying Classifications

Classification Nodes can be applied to Item Templates, Items and Slots. Most provide a way of creating new Classification Nodes in the same request.


### Templates

Single Classification Nodes may be applied to Item Templates:

```bash
  curl --request POST 'https://sandbox.meeco.me/item_templates' \
       -H "authorization: Bearer $VAULT_ACCESS_TOKEN" \
       -H 'content-type: application/json' \
       -H 'Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY' \
       --data \
'{
  "label": "Some Template",
  "classification_scheme_name": "tag",
  "classification_node_name": "new_node",
  "slots_attributes": []
}'
```

Note that both `classification_scheme_name` and `classification_node_name` are required, and both must exist.

Result

```json
{
  "item_template": {
    "id": "78724525-9aed-4156-90c5-447198aa818b",
    "name": "some_template",
    "description": null,
    "ordinal": 0,
    "visible": true,
    "user_id": "e414dc7a-b6f1-4fb6-9481-41d7be5c8785",
    "updated_at": "2020-10-07T04:13:01.305Z",
    "label": "Some Template",
    "slot_ids": [],
    "classification_node_ids": [
      "15ab7625-11f5-4518-99eb-e985c24414ad"
    ],
    "background_color": null,
    "image": null
  },
  "slots": [],
  "classification_nodes": [
    {
      "id": "15ab7625-11f5-4518-99eb-e985c24414ad",
      "background_color": null,
      "description": null,
      "image": null,
      "label": "New Tag",
      "name": "new_tag",
      "ordinal": 0,
      "scheme": "tag"
    }
  ],
  "attachments": [],
  "thumbnails": []
}
```

This has two effects. First, the Item Template can be found via its Classification Node or Scheme name, e.g. queries `GET /vault/item_templates?by_classification=tag`, or `GET /vault/item_templates?by_classification=new_tag` should include the template. Second, new Items created with the Item Template will be classified with the Classification Node.

```bash
  curl --request POST "https://sandbox.meeco.me/vault/items" \
       -H "content-type: application/json" \
       -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
       -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
       --data \
'{
  "item": {
    "label": "Fun example",
    "slots_attributes": [
      { "name": "bar" }
    ]
  },
  "template_name": "some_template"
}'
```

```json
{
  "item": {
    "id": "658ca227-b61b-4ce0-846d-baaf4cc17880",
    "name": "fun_example",
    "label": "Fun example",
    "description": null,
    "created_at": "2020-10-07T04:13:52.430Z",
    "item_template_id": "78724525-9aed-4156-90c5-447198aa818b",
    "classification_node_ids": [
      "15ab7625-11f5-4518-99eb-e985c24414ad"
    ],
    "association_ids": [],
    "associations_to_ids": [],
    "slot_ids": [
      "932e1f7e-10b5-4658-95b8-d5dff164bf37"
    ],
    "own": true,
    "...": "..."
  },
  "associations": [],
  "associations_to": [],
  "classification_nodes": [
    {
      "id": "15ab7625-11f5-4518-99eb-e985c24414ad",
      "background_color": null,
      "description": null,
      "image": null,
      "label": "New Tag",
      "name": "new_tag",
      "ordinal": 0,
      "scheme": "tag"
    }
  ],
  "slots": [
    {
      "id": "932e1f7e-10b5-4658-95b8-d5dff164bf37",
      "name": "bar",
      "classification_node_ids": [],
      "item_id": "658ca227-b61b-4ce0-846d-baaf4cc17880",
      "...": "..."
    }
  ],
  "attachments": [],
  "thumbnails": []
}
```


### Items

Classification nodes are added to Items by passing `classification_nodes_attributes`, just like adding Slots. If the name and scheme match an existing Classification Node, it is used, otherwise a new one is created.

Classification Node can be applied to both the Item and the Slots it contains. Unlike Item Templates, Items may have multiple classifications.

```bash
  curl --request POST "https://sandbox.meeco.me/vault/items" \
       -H "content-type: application/json" \
       -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
       -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
       --data \
'{
    "item": {
      "label": "item class",
      "classification_nodes_attributes": [
        {
          "name": "my_new_tag",
          "scheme": "tag"
        }
      ],
      "slots_attributes": []
    },
    "template_name": "some_template"
  }'
```

```json
{
  "item": {
    "id": "ff04474c-f661-4f71-b5a3-c40452c30e3b",
    "name": "item_class",
    "label": "item class",
    "description": null,
    "created_at": "2020-10-07T04:55:54.953Z",
    "item_template_id": "01bbb6a4-7466-423f-afdb-2d2f314011c4",
    "ordinal": 1,
    "visible": true,
    "updated_at": "2020-10-07T04:55:54.987Z",
    "item_template_label": "Some Template",
    "owner_id": "e414dc7a-b6f1-4fb6-9481-41d7be5c8785",
    "share_id": null,
    "classification_node_ids": [
      "a9b7b318-2e22-4969-a521-e6d43d882bf6"
    ],
    "association_ids": [],
    "associations_to_ids": [],
    "slot_ids": [],
    "own": true,
    "...": "..."
  },
  "associations": [],
  "associations_to": [],
  "classification_nodes": [
    {
      "id": "a9b7b318-2e22-4969-a521-e6d43d882bf6",
      "background_color": null,
      "description": "Hi There this is my Tag!",
      "image": null,
      "label": "My New Tag",
      "name": "my_new_tag",
      "ordinal": 0,
      "scheme": "tag"
    }
  ],
  "slots": [],
  "attachments": [],
  "thumbnails": []
}
```

Note that even if the Classification Node `name` property matches an existing Node, but other fields don't, then a new Classification Node will be created. The `scheme` property must match an existing Scheme.

Classification Nodes can be added to Slots too:

```bash
    curl --request POST "https://sandbox.meeco.me/items" \
         -H "content-type: application/json" \
         -H "Authorization: Bearer $VAULT_ACCESS_TOKEN" \
         -H "Meeco-Subscription-Key: $API_SUBSCRIPTION_KEY" \
         --data \
 '{
  "item": {
    "label": "Another Item",
    "slots_attributes": [
      {
        "name": "new_slot",
        "description": "Some Slot",
        "slot_type_name": "key_value",
        "label": "New Slot",
        "classification_nodes_attributes": [
          {
            "label": "A New Tag",
            "description": "Tag For The Slot",
            "scheme": "tag"
          },
          {
            "name": "my_new_tag",
            "scheme": "tag"
          }
        ]
      }
    ]
  },
  "template_name": "some_template"
}'
```

In this case the `new_slot` receives both an existing Classification Node and a new one.

```json
{
  "item": {
    "id": "bfa4c439-fc34-41e6-aba2-12191061ccb2",
    "name": "another_item",
    "label": "Another Item",
    "description": null,
    "created_at": "2020-10-07T05:15:42.039Z",
    "item_template_id": "01bbb6a4-7466-423f-afdb-2d2f314011c4",
    "ordinal": 1,
    "updated_at": "2020-10-07T05:15:42.131Z",
    "item_template_label": "Some Template",
    "owner_id": "e414dc7a-b6f1-4fb6-9481-41d7be5c8785",
    "classification_node_ids": [],
    "association_ids": [],
    "associations_to_ids": [],
    "slot_ids": [
      "636faed0-11f2-4cc4-876e-9ce1118381c8"
    ],
    "own": true,
    "...": "..."
  },
  "associations": [],
  "associations_to": [],
  "classification_nodes": [
    {
      "id": "ec6a92a2-5578-49cf-b7de-23f7a7cf8081",
      "background_color": null,
      "description": "Tag For The Slot",
      "image": null,
      "label": "A New Tag",
      "name": "a_new_tag",
      "ordinal": 0,
      "scheme": "tag"
    },
    {
      "id": "8ca80da9-53fa-4762-9b3b-857153f2dea0",
      "background_color": null,
      "description": null,
      "image": null,
      "label": "My new tag",
      "name": "my_new_tag",
      "ordinal": 0,
      "scheme": "tag"
    }
  ],
  "slots": [
    {
      "id": "636faed0-11f2-4cc4-876e-9ce1118381c8",
      "name": "new_slot",
      "description": "Some Slot",
      "classification_node_ids": [
        "ec6a92a2-5578-49cf-b7de-23f7a7cf8081",
        "8ca80da9-53fa-4762-9b3b-857153f2dea0"
      ],
      "item_id": "bfa4c439-fc34-41e6-aba2-12191061ccb2",
      "slot_type_name": "key_value",
      "label": "New Slot",
      "...": "..."
    }
  ],
  "attachments": [],
  "thumbnails": []
}
```


### Slots

Slots are usually given Classification Nodes via `POST /vault/items`, but you can add a classification to an existing Slot using `PUT /vault/slots/{id}`.

Slots cannot be searched by Classification Node or Scheme.

Some Slots have the type `classification_node`. The intent is that the owning Item will be classified with that node. Usually this is done within an app.
