```bash
meeco users:create -p password > .riker.yaml
meeco users:create -p password > .homer.yaml
```

delegation-role options are `owner`, `admin`, and `reader`

```bash
meeco delegations:create-invitation -a .riker.yaml Homer reader > .delegation-invitation.yaml
```

```bash
meeco delegations:accept-invitation -a .homer.yaml -c .delegation-invitation.yaml Riker > .delegation-connection.yaml
```

```bash
meeco templates:list -a .homer.yaml > .delegation-connection.yaml
```




meeco users:create -p password > .riker.yaml;
meeco users:create -p password > .homer.yaml;

meeco items:create-config vehicle -a .riker.yaml > .riker-vehicle-config.yaml

meeco items:create -i .riker-vehicle-config.yaml -a .riker.yaml > .riker-vehicle.yaml


meeco delegations:create-invitation -a .riker.yaml Homer reader > .delegation-invitation.yaml;
meeco delegations:accept-invitation -a .homer.yaml -c .delegation-invitation.yaml Riker > .delegation-connection.yaml;

# meeco items:list --delegationId <USER_ID_OF_DELEGATE_CONNECTION> -a .homer.yaml
meeco items:list --delegationId 07d5acf5-451f-40db-9aa0-86561d4084cb -a .homer.yaml

# meeco delegations:share-kek <CONNECTION_OTHER_USER_ID> -a .riker.yaml
meeco delegations:share-kek 1285708b-1650-44f8-875b-7f8aa64fa7cf -a .riker.yaml

# meeco delegations:accept-kek <CONNECTION_OWN_ID> -a .homer.yaml
meeco delegations:accept-kek 4db49b3b-ade1-48c7-be98-03eefc1093c8 -a .homer.yaml