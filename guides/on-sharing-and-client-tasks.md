# On-sharing and Client Tasks

## Share Terms

When sharing an item a block of text can be added to the share, there is no required structure for these terms but something like the following should give you some idea of how it might be used.

> I have allowed on-sharing on this item but before any on-sharing taks place call me on my phone to confirm it with me.  
or
> This information on this item is being shared for the express purpose of applying for a home loan, it is not to be shared with anyone for the purposes of marketing or analysis.  
or
> This information contains non-public business information and is for the eyes of Anthony Edward Stark only.

## Share Acceptance

When creating a share there is an option to set whether acceptance of the terms and share is required, the settings allowed are "acceptance_required" and  "acceptance_not_required". Before the recipient of the share can view the data they must explicitly accept the share and it's terms via a second API call.

## Sharing Mode

When a user shares an item with another user there is an option to allow or dis-allow sharing of that item with another person, this is called the "sharing mode". The sharing mode currently has two options available on it, either "owner" or "everyone". The "owner" sharing mode means the share can not be on-shared to another user. The "anyone" option means that the item can be on-shared to anyone. While the system will allow any on-sharing to happen when the sharing mode is set to "anyone" it is also important to check the "terms" that have set on the share.

## Client Tasks

Due to use of e2e (end to end) encryption the client (client application) is the only place where data can be decrypted and re-encrypted. Sometimes there are tasks that do not need to happen right away when an action takes place but they will need to be done on the client at some point. An example of this is updating shares, for example...  

If a user1 has shared an item with user2 and some time later user1 changes the data in the shared item the share will also need to be updated.  
The data in user1's items is encrypted with a DEK (data encryption key) that only user1 has access to, this means the server can not re-encrypt the data with a shared DEK on behalf of user1, instead the server creates a `ClientTask`.  
Periodically user1's client will check to see if there are any `ClientTask`s that need to be executed.  
User1's client will pick up the `ClientTask` of type `update_shares` which will tell it to download and decrypt the modified item then re-encrypt the data with a shared DEK and update the share with the new data.

## Example of On-sharing and Executing Client Tasks

(it's recommended to complete the getting-started/quickstart guide before follwing this example)

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

Share that item from Alice to Bob, with some share `terms`, `acceptance_required`, and sharing mode of `anyone`

```bash
meeco shares:create-config -i $VEHICLE_ITEM_ID -f .alice.yaml -c $CONNECTION_1_FROM_USER_ID > .share1-config.yaml
meeco shares:create \
    -config .share1-config.yaml \
    --sharing_mode="anyone" \
    --terms="You may not use this information for advertising" \
    --acceptance_required="acceptance_required"
```
