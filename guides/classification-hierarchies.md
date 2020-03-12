# Classification Hierarchies

The Meeco platform has a very flexible way to tag information. Instead of having a traditional simple flat list of tags the system can be configured to have multiple independent classifications. These classification are called _classification schemes_. Each classification scheme is a tree and consists of _classification nodes_.

All classification nodes can be queried by `GET /v2/global/classification_nodes`:

```bash
curl -X GET \
  https://sandbox.meeco.me/vault/classification_nodes \
  -H 'Authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  -H 'Content-Type: application/json'
```

You can modify the request with the following parameters:

- `page` and `per` which allow for pagination of results
- `scheme_name`
- `by_name` - a "LIKE" search which will return results for partial matches - i.e. 'fin' will return 'financial'

The response will look like:

```json
{
  "classification_nodes": [
      {
          "id": "53ad2133-2aec-4938-92a1-d24219c411e4",
          "background_color": null,
          "description": null,
          "image": "https://api-sandbox.meeco.me/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
          "label": "Warranty",
          "name": "warranty",
          "ordinal": 13,
          "scheme": "esafe_tags"
      },
    ...
  ],
  "meta": {
    "pages": null,
    "total_count": null
  }
}
```
You can install the command line `jq` from [here](https://stedolan.github.io/jq/download/) which will help filter the json responses for the following requests.

Let's see which schemes there are:

```sh
curl -X GET \
    'https://sandbox.meeco.me/vault/classification_nodes' \
    -H 'Authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
    -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
    -H 'Content-Type: application/json' | jq | grep scheme | sort | uniq
```

Response:

```json
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

```sh
curl -X GET \
    'https://sandbox.meeco.me/vault/classification_nodes?scheme_name=esafe_tags' \
    -H 'Authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
    -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
    -H 'Content-Type: application/json' | jq | grep scheme | sort | uniq
```

Response:

```json
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

```sh
curl -X GET \
  'https://sandbox.meeco.me/vault/classification_nodes?by_name=pe' \
  -H 'Authorization: Bearer VAULT_ACCESS_TOKEN_FROM_CLI_GENERATED_USER' \
  -H 'Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY' \
  -H 'Content-Type: application/json' | jq
```

Response:

```json
{
  "classification_nodes": [
    {
      "id": "bc466309-1f41-414a-869c-6e28e450d72f",
      "background_color": "#FF00FF",
      "description": null,
      "image": "https://api-sandbox.meeco.me/images/847a5928-90e9-4569-bc5b-ca37e5d65df0",
      "label": "People",
      "name": "people",
      "ordinal": 2,
      "scheme": "esafe"
    },
    {
      "id": "e2a29d20-5ffd-41da-9085-70dc49ed1978",
      "background_color": null,
      "description": null,
      "image": "https://api-sandbox.meeco.me/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
      "label": "Personal",
      "name": "personal",
      "ordinal": 6,
      "scheme": "esafe_tags"
    },
    {
      "id": "a807d605-abf2-49a6-9593-930ce8adb59b",
      "background_color": "#FF00FF",
      "description": null,
      "image": "https://api-sandbox.meeco.me/images/0c859388-fd28-4672-9e6e-3361ef8444ab",
      "label": "Pets",
      "name": "pets",
      "ordinal": 7,
      "scheme": "esafe"
    },
    {
      "id": "d85fe895-063f-4cba-9af1-531d1f324c3d",
      "background_color": null,
      "description": null,
      "image": "https://api-sandbox.meeco.me/images/d02f5cfc-b1f2-46be-805c-5a8e6702b55c",
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
