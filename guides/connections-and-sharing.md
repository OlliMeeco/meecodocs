---
description: How to create a connection between you and another user to share data
---

# Connections and Sharing - CLI

After successfully creating an item in your user's Vault from the [Quickstart section](../getting-started/quickstart.md), it's now time to create another user.

```bash
meeco users:create -p supersecretpassword > .user_2.yaml
```

We used the same password as in the Quickstart example, in case you were wondering.

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

