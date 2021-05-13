# On-sharing and Client Tasks

## Share Terms

When sharing an item, a block of text can be added to the share, there is no required structure for these terms but something like the following should give you some idea of how it might be used.

> I have allowed on-sharing on this item but before any on-sharing takes place call me on my phone to confirm it with me.

or

> This information on this item is being shared for the express purpose of applying for a home loan, it is not to be shared with anyone for the purposes of marketing or analysis.

or

> This information contains non-public business information and is for the eyes of Anthony Edward Stark only.

## Share Acceptance

When creating a share there is an option to set whether acceptance of the terms and share is required, the settings allowed are `acceptance_required` and `acceptance_not_required`. Before the recipient of the share can view the data they must explicitly accept the share and it's terms via a second API call. For convenience when using the cli we have automatically set this feild to `acceptance_required` when any terms are specified.

## Sharing Mode

When a user shares an item with another user there is an option to allow or dis-allow sharing of that item with another person, this is called the "sharing mode". The sharing mode currently has two options available on it, either `owner` or `anyone`. The `owner` sharing mode means the share can not be on-shared to another user. The `anyone` option means that the item can be on-shared to anyone. While the system will allow any on-sharing to happen when the sharing mode is set to `anyone` it is also important to check the `terms` that have set on the share. For convenience in the cli we have added the flag `--onshare` which will set the `sharing_mode` to `anyone` if it is present and set it to `owner` if it is not present.

## Client Tasks

Due to use of e2e (end to end) encryption the client (client application) is the only place where data can be decrypted and re-encrypted. Sometimes there are tasks that do not need to happen right away when an action takes place but they will need to be done on the client at some point. An example of this is updating shares, for example...

If a Alice has shared an item with Bob and some time later Alice changes the data in the shared item the share will also need to be updated.  
The data in Alice's items is encrypted with a DEK (data encryption key) that only Alice has access to, this means the server can not re-encrypt the data with a shared DEK on behalf of Alice, instead the server creates a `ClientTask`.  
Periodically Alice's client will check to see if there are any `ClientTask`s that need to be executed.  
Alice's client will pick up the `ClientTask` of type `update_shares` which will tell it to download and decrypt the modified item then re-encrypt the data with a shared DEK and update the share with the new data.

## Example of On-sharing and Executing Client Tasks

(it's recommended to complete the getting-started/quickstart guide before follwing this example)

(NOTE: each user will see the shared item as different `item_id`, when referencing the item by id be sure to use the `item_id` applicable to the user making the request)

First lets create three users.

```bash
meeco users:create -p supersecretpassword > .alice.yaml
meeco users:create -p supersecretpassword > .bob.yaml
meeco users:create -p supersecretpassword > .carlos.yaml
```

Next lets connect Alice to Bob, then Bob to Carlos.

```bash
meeco connections:create-config --from .alice.yaml --to .bob.yaml > .connection1_config.yaml
meeco connections:create -c .connection1_config.yaml > .connection1.yaml
meeco connections:create-config --from .bob.yaml --to .carlos.yaml > .connection2_config.yaml
meeco connections:create -c .connection2_config.yaml > .connection2.yaml
```

We create an item for Alice.

```bash
meeco items:create-config vehicle -a .alice.yaml > .vehicle_config.yaml
# open up the .vehicle.yaml and add an item label and other data
meeco items:create -i .vehicle_config.yaml -a .alice.yaml > .vehicle.yaml
```

Share that item from Alice to Bob, with some share `terms`, `acceptance_required`, and sharing mode of `anyone`.

```bash
meeco shares:create-config -i .vehicle.yaml -f .alice.yaml -c .connection1.yaml > .share1-config.yaml
meeco shares:create \
    --config .share1-config.yaml \
    --onshare \
    --terms="You may not use this information for advertising" \
    > .share1.yaml
```

Read the share as Bob.

```bash
meeco items:list -a .bob.yaml
```

You will get a result something like the following...

```yaml
kind: Items
spec:
  - id: 8e26d96f-7c15-444d-97a5-30e39b418c9d
    own: false
    label: DeLorean
    description: null
    created_at: 2020-10-12T05:52:45.395Z
    item_template_id: 311422ef-9546-404b-92c4-483a7ce3ebd0
    item_template_label: Vehicle
    slot_ids:
      - 642ea490-ccce-49cc-9fef-d5d9c6668549
      ...
    me: false
    background_color: null
    original_id: dc66140c-3572-49b8-ad97-ec6de21827ba
    owner_id: 94c27129-e179-4c69-a7f1-94438b920541
    share_id: b358bc99-89f1-48f3-8b84-8dad0516643e
    ...
```

Note the share_id from above.

Bob can have a look at the details and specifically the `terms` of the share by getting the share's details with the command.

```bash
meeco shares:get-incoming -a .bob.yaml $EXISTING_SHARE_ID
```

Note that the item slot details are not available to Bob until he accepts the share and it's terms.

Next Bob will accept the share from Alice.

```bash
meeco shares:accept -y $EXISTING_SHARE_ID -a .bob.yaml
```

Bob can now pull down the item's details. (Note: the item id must come from the item when performing the items:list command, the item_id in the shares:accept command will not work).

```bash
meeco items:get 8e26d96f-7c15-444d-97a5-30e39b418c9d -a .bob.yaml > .shared-to-bob-item.yaml
# meeco items:get <item id> -a <auth file>
```

Now Bob can share this item with Carlos.

```bash
meeco shares:create-config -i .shared-to-bob-item.yaml -f .bob.yaml -c .connection2.yaml > .share2-config.yaml
meeco shares:create \
    --config .share2-config.yaml \
    --terms="You may not use this information for advertising" \
    > .share2.yaml
```

Carlos can now see the share.

```bash
meeco items:list -a .carlos.yaml
```

Returning something like...

```yaml
kind: Items
spec:
  - id: 59cb375f-6188-46fd-bdd6-ba730ae3282c
    own: false
    label: DeLorean
    description: null
    created_at: 2020-10-13T05:41:02.376Z
    item_template_id: 311422ef-9546-404b-92c4-483a7ce3ebd0
    item_template_label: Vehicle
    slot_ids:
      - e90e38f2-65d7-4203-b74f-0831dab393c1
      ...
    original_id: dc66140c-3572-49b8-ad97-ec6de21827ba
    owner_id: 94c27129-e179-4c69-a7f1-94438b920541
    share_id: 47b22868-99c1-4b8b-b9d5-e0ec0830c31c
```

Noting the share_id and item_id from the above command.

Accept it.

```bash
meeco shares:accept $EXISTING_SHARE2_ID -a .carlos.yaml
```

And view it.

```bash
meeco shares:get-incoming $CARLOS_SHARED_INCOMING_SHARE_ID -a .carlos.yaml
# or
meeco items:get $CARLOS_SHARED_INCOMING_ITEM_ID -a .carlos.yaml
```

Getting the output something like...

```yaml
kind: Item
spec:
  id: 59cb375f-6188-46fd-bdd6-ba730ae3282c
  own: false
  label: DeLorean
  item_template_id: 311422ef-9546-404b-92c4-483a7ce3ebd0
  item_template_label: Vehicle
  slot_ids:
    - 81cc655f-5ae8-4a7a-afc0-3d186ab3f736
    ...
  me: false
  background_color: null
  original_id: dc66140c-3572-49b8-ad97-ec6de21827ba
  owner_id: 94c27129-e179-4c69-a7f1-94438b920541
  share_id: 47b22868-99c1-4b8b-b9d5-e0ec0830c31c
  slots:
    - id: 81cc655f-5ae8-4a7a-afc0-3d186ab3f736
      own: false
      share_id: 47b22868-99c1-4b8b-b9d5-e0ec0830c31c
      name: licence_plate
      item_id: 59cb375f-6188-46fd-bdd6-ba730ae3282c
      slot_type_name: key_value
      encrypted_value: Aes256Gcm.AjR9r_An-mcqLla1dcGL.QUAAAAAFaXYADAAAAACcR_n2XVORiZ2wRjgFYXQAEAAAAACCNE5L6Gqq-4Zmm5Y8AgVEAmFkAAUAAABub25lAAA=
      encrypted_value_verification_key: Aes256Gcm.f4af1rELcDkDJQv7wPsfvOOVbnI6YyQlsnYLdDdekAZ2AcXm6Oq7WrgGytc3gUbvjnWvmkzAnWO2s2Kya0ZxLQ==.QUAAAAAFaXYADAAAAABQ346yXewWcSdSdBEFYXQAEAAAAACyZph19u-eZ-BWMPhrOqP4AmFkAAUAAABub25lAAA=
      value_verification_hash: d8c6e5f28837906505fa9e6a4740c8ddd5dddbe4b51675ae3daabef4a27d701b
      label: Vehicle registration number
      original_id: 5503d256-19d6-45f8-b76b-9605f977cd3f
      owner_id: 94c27129-e179-4c69-a7f1-94438b920541
      value: NotACatDefsACar
      value_verification_key: "5ÁÑ\x1a=Ù¯\a\a\x1f|1\v§ì¨Nqe¡b\x05Ç\rrvý[nê\x05À\x11ÞBïÇí\eé\x14y\
        há_éûCz)êö\0Û]³"
      ...
    ...
  thumbnails: []
  attachments: []
  ...
```

So now we have an Item on-shared from Alice to Bob to Carlos. What if Alice now wants to update the item and get Bob and Carlos the updated data?

First Alice updates the item.

```bash
meeco items:get $VEHICLE_ITEM_ID -a .alice.yaml > .existing_vehicle_item.yaml
# edit the .existing_vehicle_item.yaml file, specifically the `value` fields of the `slots` and/or the `label` field of the `item` then...
meeco items:update -i .existing_vehicle_item.yaml -a .alice.yaml
```

Ok, the item has been updated at this stage but not the shares.
Note that the last line of the `items:update` command showed a comment saying

```bash
# Item updated. There are 1 outstanding client tasks. Todo: 1 & InProgress: 0
```

If we request the list of Client tasks with the following command...

```bash
meeco client-task-queue:list -a .alice.yaml
```

We will see a list of ClientTasks that are outstanding something like the following.

```yaml
kind: ClientTaskQueue
spec:
  - id: 0de4fb1a-6c43-4ca5-8880-c5187df3972b
    state: todo
    work_type: update_item_shares
    target_id: dc66140c-3572-49b8-ad97-ec6de21827ba
    additional_options: {}
    last_state_transition_at: null
    report: {}
    created_at: 2020-10-13T06:02:55.028Z
```

What we need to do next is re-encrypt all the new data with a new DEK to share with both Bob and Carlos.

We can do this by running the command.

```bash
meeco client-task-queue:run-batch -a .alice.yaml
```

You will see the output something like the following.

```yaml
completedTasks:
  - id: 0de4fb1a-6c43-4ca5-8880-c5187df3972b
    state: todo
    work_type: update_item_shares
    target_id: dc66140c-3572-49b8-ad97-ec6de21827ba
    additional_options: {}
    last_state_transition_at: null
    report: {}
    created_at: 2020-10-13T06:02:55.028Z
failedTasks: []
```

Now Carlos and Bob have the updated shared data, you can check that by running the commands.

```bash
meeco shares:get-incoming $BOB_SHARED_INCOMING_SHARE_ID -a .carlos.yaml
or
meeco items:get $BOB_SHARED_INCOMING_ITEM_ID -a .bob.yaml

meeco shares:get-incoming $CARLOS_SHARED_INCOMING_SHARE_ID -a .carlos.yaml
or
meeco items:get $CARLOS_SHARED_INCOMING_ITEM_ID -a .carlos.yaml
```

Hopefully this guide has given you a decent high level overview of how the process works.  
To dig further into the functionality why not try checking out how the CLI (https://github.com/Meeco/js-sdk/tree/master/packages/cli) uses the underlying SDK (https://github.com/Meeco/js-sdk/tree/master/packages/sdk).
