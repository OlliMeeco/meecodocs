# Account Delegation

## What is account delegation

Account delegation provides access and/or control over your private encrypted data to another user. The intended purpose for this feature is to allow people you trust full access. In almost all cases we recommend using Shares and On-Shares to give access to other users as it gives much more granular control over the data shared to another user. An example where you might use a feature like this is where a user is helping a family member (be that a child or an elderly parent) who requires assistance.

## Setting up delegation

### Setting up the Scenario

First lets create two new users to set up a delegation between.

```bash
meeco users:create -p password > .riker.yaml
meeco users:create -p password > .homer.yaml
```

Next lets create an item in Riker's account so we can later test to make sure Homer has access to it.

```bash
# List out available templates
meeco templates:list -a .riker.yaml
# Create an item config file from one of the chosen templates
meeco items:create-config vehicle -a .riker.yaml > .riker-vehicle-config.yaml
# Extra step, open up the above item config yaml file and modify it in your chosen text editor
# Create the item using the above item config yaml
meeco items:create -i .riker-vehicle-config.yaml -a .riker.yaml > .riker-vehicle.yaml
```

### Creating the Delegation Connection

Next lets get Riker to create a delegation connection invitation with the delegation role `reader`. (delegation-role options are `owner`, `admin`, and `reader`)

```bash
meeco delegations:create-invitation -a .riker.yaml Homer reader > .delegation-invitation.yaml
```

Homer can now accept that delegation connection invitation from Riker and the delegation connection will have been created.

```bash
meeco delegations:accept-invitation -a .homer.yaml -c .delegation-invitation.yaml Riker > .delegation-connection.yaml
```

### Sharing the Key Encryption Key

While the connection has been created There are still a couple more steps to go before the delegation has been fully set up. These steps are for sharing Riker's Key Encryption Key (KEK) to Homer. First Riker must Encrypt his KEK with Homer's public key and send it.

```bash
# meeco delegations:share-kek <OTHER_USER_CONNECTION_ID> -a .riker.yaml
meeco delegations:share-kek 53cbd900-6657-40a0-9fae-ad8ac20078f4 -a .riker.yaml
```

Next Homer accepts Riker's KEK, decrypting it with his private key, then storing it for access later under his own KEK.

```bash
# meeco delegations:accept-kek <CONNECTION_OWN_ID> -a .homer.yaml
meeco delegations:accept-kek 099ebb9b-b49f-4c20-8606-b92b7dcc8ea6 -a .homer.yaml
```

### Reading User data as a delegate user

First Homer can pre-load Riker's KEK and Private DEK should for decrypting the data.

```bash
# meeco delegations:load-auth-config --delegationId <USER_ID_OF_OTHER_USER_CONNECTION> -a .homer.yaml > .homer-with-riker-delegation.yaml
meeco delegations:load-auth-config --delegationId c0181886-20de-45de-b4b5-b614f92f2440 -a .homer.yaml > .homer-with-riker-delegation.yaml
```

Next Homer can list out the Riker's items.

```bash
# meeco items:list --delegationId <USER_ID_OF_OTHER_USER_CONNECTION> -a .homer.yaml
meeco items:list --delegationId c0181886-20de-45de-b4b5-b614f92f2440 -a .homer-with-riker-delegation.yaml
```

Then, taking note of the item id from the items:list command, finally Homer can view the item Riker created earlier.

```bash
# meeco items:get --delegationId <USER_ID_OF_OTHER_USER_CONNECTION> <ITEM_)I> -a .homer-with-riker-delegation.yaml
meeco items:get --delegationId c0181886-20de-45de-b4b5-b614f92f2440 0f0dc0d3-d7f4-42e6-8b46-e41bf3a51d2a -a .homer-with-riker-delegation.yaml
```
