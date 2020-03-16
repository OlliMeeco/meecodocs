# Classification Hierarchies

The Meeco platform has a very flexible way to tag information. Instead of having a traditional simple flat list of tags the system can be configured to have multiple independant classifications. These classification are called _classification schemes_. Each classification scheme is a tree and consists of _classification nodes_.

All classification nodes can be queried by `GET /v2/global/classification_nodes`:

```bash
curl -X GET \
  https://meeco-api-dev.azure-api.net/vault/classification_nodes \
  -H 'Authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -H 'Content-Type: application/json'
```

You can modify the request with the following parameters:

* `page` and `per` which allow for pagination of results
* `scheme_name`
* `by_name` - a "LIKE" search which will return results for partial matches - i.e. 'fin' will return 'financial'

The response will look like:

```javascript
{
  "classification_nodes": [
    {
      "id": "7f7b94f6-5a25-4de1-a2d9-2c0822aa69aa",
      "background_color": null,
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
      "label": "Meeco Templates",
      "name": "meeco_templates",
      "ordinal": 1,
      "scheme": "meeco"
    },
    ...
  ],
  "meta": {
    "pages": null,
    "total_count": null
  }
}
```

Let's see which schemes there are:

```bash
curl -X GET \
    'https://meeco-api-dev.azure-api.net/vault/classification_nodes' \
    -H 'Authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
    -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
    -H 'Content-Type: application/json' | jq | grep scheme | sort | uniq
```

Response:

```javascript
  "scheme": "esafe",
  "scheme": "esafe_tags",
  "scheme": "meecong",
  "scheme": "program",
  "scheme": "program_role",
  "scheme": "story_category",
  "scheme": "story_relationship",
  "scheme": "tag",
```

It is possible to query only nodes belonging to a certain classification scheme:

```bash
curl -X GET \
    'https://meeco-api-dev.azure-api.net/vault/classification_nodes?scheme_name=esafe_tags' \
    -H 'Authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
    -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
    -H 'Content-Type: application/json' | jq | grep scheme | sort | uniq
```

Response:

```javascript
  "name": "family",
  "name": "work",
  "name": "fun",
  "name": "holiday",
  "name": "shopping",
  "name": "personal",
  "name": "health",
  "name": "pets",
  "name": "vehicle",
  "name": "financial",
  "name": "education",
  "name": "subscription",
  "name": "warranty",
```

It is also possible to search nodes by partial names:

```bash
curl -X GET \
  'https://meeco-api-dev.azure-api.net/vault/classification_nodes?by_name=pe' \
  -H 'Authorization: Bearer DECRYPTED_VAULT_ADMISSION_TOKEN_FROM_DOCUMENT_2_STEP_11' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -H 'Content-Type: application/json' | jq
```

Response:

```javascript
{
  "classification_nodes": [
    {
      "id": "bc466309-1f41-414a-869c-6e28e450d72f",
      "background_color": "#FF00FF",
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/847a5928-90e9-4569-bc5b-ca37e5d65df0",
      "label": "People",
      "name": "people",
      "ordinal": 2,
      "scheme": "esafe"
    },
    {
      "id": "a807d605-abf2-49a6-9593-930ce8adb59b",
      "background_color": "#FF00FF",
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/0c859388-fd28-4672-9e6e-3361ef8444ab",
      "label": "Pets",
      "name": "pets",
      "ordinal": 7,
      "scheme": "esafe"
    },
    {
      "id": "e2a29d20-5ffd-41da-9085-70dc49ed1978",
      "background_color": null,
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
      "label": "Personal",
      "name": "personal",
      "ordinal": 6,
      "scheme": "esafe_tags"
    },
    {
      "id": "d85fe895-063f-4cba-9af1-531d1f324c3d",
      "background_color": null,
      "description": null,
      "image": "https://meeco-api-dev.azure-api.net/vault/global/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
      "label": "Pets",
      "name": "pets",
      "ordinal": 8,
      "scheme": "esafe_tags"
    }
  ],
  "meta": {
    "pages": null,
    "total_count": null
  }
}
```

