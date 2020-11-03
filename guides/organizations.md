# Organizations

## Overview

The Organizations feature adds a way for your organization to connect with users and allows you to:

* Share the personal data your organization has about a given user with them in a secure and private
channel (to comply with GDPR for example).
* Have data shared with your organization from a given user in a secure and private channel
with explicit consent and have the data updated by the user automatically updated in your
company's systems.

## Setting up an Organization

Assuming you have followed the guide from the quickstart section [Quickstart section](../getting-started/quickstart.md)
you should have a file for your user `.alice.yaml`. For the purposes of this example let's also create a second user
`.bob.yaml`.

```bash
meeco users:create -p supersecretpassword > .bob.yaml
```
  
The next thing you are going to want to do is create the organization. Create a file called `.organization-config.yaml`
with the content something like the following.

```yaml
kind: Organization
metadata:
spec:
  name: SuperData Inc.
  description: My super data handling organization
  url: https://superdata.example.com
  email: admin@superdata.example.com
```

Then run the following command

```bash
meeco organizations:create -a .alice.yaml -c .organization-config.yaml > .organization.yaml
```

The output `.organization.yaml` file will look something like the following.

```yaml
kind: Organization
metadata:
  privateKey: "-----BEGIN RSA PRIVATE KEY-----\r
    ...
    -----END RSA PRIVATE KEY-----\r\n"
  publicKey: "-----BEGIN PUBLIC KEY-----\r
    ...
    -----END PUBLIC KEY-----\r\n"
spec:
  id: cc656956-6fb3-4bd1-a906-81c85dd6cb7e
  name: SuperData Inc.
  description: My super data handling organization
  url: https://superdata.example.com
  email: admin@superdata.example.com
  status: requested
  requestor_id: 33bd39f9-980f-4ea6-92d3-6a28a218e6ac
  validated_by_id: null
  agent_id: null
  validated_at: null
  created_at: 2020-10-29T03:12:18.153Z
```

Notice how the `status` field is set to `requested` at this stage, this means our team has been
notified of your intent to create the organization and will review and get in contact with you
via the email provided. At this stage only you and the team at Meeco can see the organization.
You can check organization was created and in requested state waiting for approval with the following
command.

```yaml
meeco organizations:list -m requested -a .alice.yaml
```

Once your organization has been approved by the team at meeco we'll let you know via email. From this
point on your organization is publicly viewable via the API in Meeco. After that you can check to see
that your organization is listed with the other publicly viewable organizations with this command.

```bash
meeco organizations:list -a .alice.yaml
```

Which outputs like the following.

```yaml
kind: Organizations
spec:
  - id: cc656956-6fb3-4bd1-a906-81c85dd6cb7e
    name: SuperData Inc.
    description: My super data handling organization
    url: https://superdata.example.com
    email: admin@superdata.example.com
    status: validated
    requestor_id: 33bd39f9-980f-4ea6-92d3-6a28a218e6ac
    validated_by_id: 33bd39f9-980f-4ea6-92d3-6a28a218e6ac
    agent_id: 79b75bec-aafa-4850-9c47-dfce2c633453
    validated_at: 2020-10-29T05:40:22.991Z
    created_at: 2020-10-29T03:12:18.153Z
  ...
```

Start adding members to your organization. To invite a user to your organization first we create an
invitation using the following command.

```bash
meeco organization-members:create-invitation -o .organization.yaml -a .alice.yaml > .org-invitation.yaml
```

Next lets have user Bob accept this invitation to become a member of the organization.

```bash
meeco organization-members:accept-invitation -i .org-invitation.yaml -a .bob.yaml > .bob-org-membership.yaml
```

You can see the list of members in the organization with the command.

```bash
meeco organization-members:list -a .alice.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e
# meeco organization-members:list -a <USER_AUTH_YAML> <ORGANIZATION_ID>
```

## Organization Services

Organization Services are what your organization can use to connect to users, share data with them, and have data
shared back with you. These services are a set of credentials that you can put into your third party systems to give
them access to meeco's services. For the purposes of this guide we will complete everything using the CLI however
we recommend you set up an application which performs these tasks either using the `@meeco/sdk` npm package or hitting
our API endpoints directly using the SDK as a guide.  
  
First lets create a service configuration file `.organization-service-config.yaml` something like the following. (Hint:
you can use the cryppo cli [https://github.com/Meeco/cryppo-cli](https://github.com/Meeco/cryppo-cli) to quickly and
easily create a keypair with the command `cryppo genkeypair -p private.pem -P public.pem`).

```yaml
kind: OrganizationService
spec:
  name: Data Sharing Service
  description: This service is for outgoing shared data from application X
  contract: A message about the contract
```

Next we create the service using that config file.

```bash
meeco organization-services:create -a .alice.yaml .organization-service-config.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e > .organization-service.yaml
# meeco organization-services:create -a <USER_AUTH_YAML> -c <ORGANIZATION_SERVICE_CONFIG_FILE> <ORGANIZATION_ID> > <OUTPUT_FILE>
```

The `.organization-service.yaml` should have output like the following.

```yaml
kind: OrganizationService
metadata:
  privateKey: "-----BEGIN RSA PRIVATE KEY-----\r
    ...
    -----END RSA PRIVATE KEY-----\r\n"
  publicKey: "-----BEGIN PUBLIC KEY-----\r
    ...
    -----END PUBLIC KEY-----\r\n"
spec:
  id: c213d93e-32a5-4e1d-96bb-0816e7eb6c74
  name: Data Sharing Service
  description: This service is for outgoing shared data from application X
  contract: A message about the contract
  status: requested
  organization_id: cc656956-6fb3-4bd1-a906-81c85dd6cb7e
  validated_by_id: null
  agent_id: null
  validated_at: null
  created_at: 2020-11-03T03:43:03.751Z
```

At this stage the Organization service has been requested and has the status `requested`. The meeco Team will again
review this OrganizationService, reach out to you via email if needed, and validate your service. Once this is done
we will notify you via email and you will be able to confirm that the organization service is now validated with the
command. The `metadata.privateKey` here should be saved as configuration for your organization service application.

```bash
meeco organization-services:get -a .alice.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e c213d93e-32a5-4e1d-96bb-0816e7eb6c74
```

The result should be something like the following, if the list is empty it means the service has not yet been validated.

```yaml
kind: OrganizationService
spec:
  id: c213d93e-32a5-4e1d-96bb-0816e7eb6c74
  name: Data Sharing Service
  description: This service is for outgoing shared data from application X
  contract: A message about the contract
  status: validated
  organization_id: cc656956-6fb3-4bd1-a906-81c85dd6cb7e
  validated_by_id: 33bd39f9-980f-4ea6-92d3-6a28a218e6ac
  agent_id: 3392a0e4-0046-40d5-a532-5bca94f1d801
  validated_at: 2020-11-03T04:51:59.724Z
  created_at: 2020-11-03T04:27:16.784Z
```

Now we have created our organization service we can request an authentication token for it.

```bash
meeco organization-services:login -a .alice.yaml -s .organization-service.yaml > .organization_service_auth.yaml
```

The `.organization_service_auth.yaml` should look like the following.

```yaml
kind: Authentication
metadata:
  data_encryption_key: ""
  key_encryption_key: ""
  keystore_access_token: ""
  passphrase_derived_key: ""
  secret: ""
  vault_access_token: ANDJk1gJpFKmwrCjNyd7
spec: {}
```

The `metadata.vault_access_token` should be saved as configuration for your organization service application. So from now
your application has a `vault_access_token` and a `private_key`, these should be set as configuration variables. But how
do we use them.  
  
Your organization first needs to connect to a user through it's organization service. To do that it will first create
an invitation.  
  
The `keypair_external_id` can be left blank in this case if we are using the same public key to connect with all users. In
some cases you may wish to use a unique public key for each user connection, in this case you should store the related private
key somewhere with reference to the user who you are connecting to.  
  
The `encrypted_recipient_name` is a record to be used by your organization only, it should identify in some way who
the invitation has been created for but we recommend not exposing any user information or an internal identifier here.

```bash
IFS='' read -r -d '' String <<EOM
  {
    "public_key": {
      "keypair_external_id": "string",
      "public_key": "-----BEGIN PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAsaPIYzlg/LApaPHHF+pw\n0R6r0zHGkRJWQypJuABSAVDfFq/eTn6q+LByt1HO5FYCHXv6O7Zt6NhaahL8sDqQ\nWPQJPENj5ktIWdjJCdY6TjTJmbDz9ICTu8y8SUWuzMH4UHBZvzA0GXN0zeC0waKc\n55FOyQNtXqxIqH5gMiZEwQ3uUc9fmAfu6XthKPNmHokTauCsI3Pi6Z31aO8Qvbyp\nFiT4a0kzHzpdtIS7Hs60bYg/hhvyB3cPytbqLFpgeGS/AZJ0e1H1Ca2b4eLUUz63\nae0sQGGFxWraw/Jnut8gYjwTlYPbJ+hRrQNrQ55jboV7fcGGgN5nR2rDDailMRMo\nPC79UZr7gJFy8VzbbJeY/TbpbLRRYeB9NR+Syi/S3W/aPnbU/+yyNcZaPqgdvQ0Y\n8voCZWbrBCTMw4Dfctoqnken0QlUj3yRmQWyeNBQ3hCefFpo9mR5fbiwUQzozLHL\nierN8LhcZj8xeH4r18ifjBV57Xeo72rtrnx6uF5blCzebv21ZZ6MqHhPaO2KoV+f\neKzaMP+ll66mEKLI311FmcoZoQwYZcUEiVrJRbOemvvE8AqSU5qFaODFdxzg8bQ/\n4PIdg6h1qWzUPw7V+PQEqF9fIsp0JJStV+YDlb6Xi+riX+XKszXYbDZ2js0aeXcX\nnCnWypargitdR558sFZ751ECAwEAAQ==\n-----END PUBLIC KEY-----\n"
    },
    "invitation": {
      "encrypted_recipient_name": "user_id_12345"
    }
  }
EOM
curl -X POST "https://sandbox.meeco.me/vault/invitations" \
  -d $DATA \
  -H "Accept: application/json" \
  -H "Meeco-Subscription-Key: Q7dFWQDGSsAE6tUo1G93Mlhn4SCOBrJe" \
  -H "Authorization: ANDJk1gJpFKmwrCjNyd7" \
  -H "Content-Type: application/json"
```
