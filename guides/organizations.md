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
`.carol.yaml`.

```bash
meeco users:create -p supersecretpassword > .carol.yaml
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

```bash
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

To start adding members to your organization you must first authenticate as the organization.

```bash
meeco organizations:login -o .organization.yaml -a .alice.yaml > .organization-auth.yaml
```

Next we have to create an invitation to become a member of the organization as the organization.

```bash
meeco organization-members:create-invitation -o .organization.yaml -a .organization-auth.yaml > .org-invitation.yaml
```

Next lets have user Carol accept this invitation to become a member of the organization.

```bash
meeco organization-members:accept-invitation -i .org-invitation.yaml -a .carol.yaml > .carol-org-membership.yaml
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
meeco organization-services:create -a .alice.yaml -c .organization-service-config.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e > .organization-service.yaml
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

At this stage the Organization service has been requested and has the status `requested`. The `metadata.privateKey` here should be saved as configuration for your organization service application.  

Before the validation of the organization service you can check on the pending services by running the command.

```bash
meeco organization-services:list -a .alice.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e
# meeco organization-services:list -a .alice.yaml <ORGANIZATION_ID>
```

You should see some output like the following.

```bash
kind: OrganizationServices
spec:
  - id: c213d93e-32a5-4e1d-96bb-0816e7eb6c74
    name: Data Sharing Service
    description: This service is for outgoing shared data from application X
    contract: null
    status: requested
    organization_id: cc656956-6fb3-4bd1-a906-81c85dd6cb7e
    validated_by_id: null
    agent_id: null
    validated_at: null
    created_at: 2021-05-10T06:14:18.968Z
```

The meeco Team will again review this OrganizationService, reach out to you via email if needed, and validate your service. Once this is done we will notify you via email and you will be able to confirm that the organization service is now validated with the command.

```bash
meeco organization-services:get -a .alice.yaml cc656956-6fb3-4bd1-a906-81c85dd6cb7e c213d93e-32a5-4e1d-96bb-0816e7eb6c74
# meeco organization-services:get -a .alice.yaml <ORGANIZATION_ID> <SERVICE_ID>
```

The result should be something like the following, if the service is not found (404) it means the service has not been validated yet.

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

Now we have created our organization service we can request an authentication token for it. (NOTE: your organization service will need to be validated before you can login as it)

```bash
meeco organization-services:login -a .alice.yaml -s .organization-service.yaml > .organization-service-auth.yaml
```

The `.organization-service-auth.yaml` should look like the following.

```yaml
kind: Authentication
metadata:
  vault_access_token: ANDJk1gJpFKmwrCjNyd7
spec: {}
```

The `metadata.vault_access_token` should be saved as configuration for your organization service application. So from now
your application has a `vault_access_token` and a `private_key`, these should be set as configuration variables. But how
do we use them.

## Connecting your Organization to Users
  
Your organization first needs to connect to a user through it's organization service. To do that it will first create
an invitation.  
  
The `keypair_external_id` can be left blank in this case if we are using the same public key to connect with all users. In
some cases you may wish to use a unique public key for each user connection, in this case you should store the related private
key somewhere with reference to the user who you are connecting to.  
  
The `encrypted_recipient_name` is a record to be used by your organization only, it should identify in some way who
the invitation has been created for. It should encrypted and serialized using a Cryppo serialization format (see
https://github.com/Meeco/cryppo-js for more details).

The `public_key` here can be unique per connection but in this case we are just going th use the keypair from the organization
service.

The `Authorization` header here should be the `vault_access_token` from the .`organization-service-auth.yaml` from above. To convert the `public_key` into the format needed for betlow you can use `yq` (https://mikefarah.gitbook.io/yq/) like so `cat .organization-service.yaml | yq -j eval`.

```bash
# first part here just writes the json to a file removing all whitespace and newlines
cat <<'EOF' |  tr -d '\n' | tr -s ' ' > data.json
  {
    "public_key": {
      "keypair_external_id": "string",
      "public_key": "-----BEGIN PUBLIC KEY-----\rMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAzao4lCviLTEg2ze8jmts\ry6iG6iKX3TGIdlcHJbFBAs0oCVp29jhlOXVfwuxX6gqplLT44rpSnYcsvKYao7ol\rt7ZadkQg/j1xm+Sw/FTyLKhNyHznclnTUXPnnvY6sHwpfaC0NBIAy1XRI8r3gqiu\rngOx4afC+PeZnrCvhjsnmR1cd/FXTWQuH8GfrqdDbH3K8ObAv4r3VT2RcUvgUEVg\r6Yg29fAF5B9Wmcwl9Kr53nryp04412QvNjlJZlilbNsmvXYnPfu4bM8kRV56iBeE\rL1OOBu65oZcBvXym3Gtd057J+0kDqGp4t76qB9DNy8n3SikvY8hyZHQsx54QBecy\rKxPETEGVWGMFHI5+UIriMr0PEF4AUE9KZWVt3bAhQPyby3Fm9dju01q9nV2qt5Cy\rZnsNui2VZ7EGDxoJPH74fsnrXX7cX8VPFgg2pxmpzmI12X1YBKdymKvCRsa+5I5S\rF2/g5pwJznx1babFSCSenmPr8eIz6Y1iJSigwr4tsUAbE1C6Vn6PEBrsmzMFhe3M\rA7YwqJvNjOZZb3Yocaf08Xn8/oISCd1ItU5W6mLW/uwRSB4cGs/D7E5mNj0bvE+1\rG94RFHSDCzIYyPtb/8dFKlwSJ3Jm2HytiN7c2dVv61KrQFRyktPHPLVR/V6r8XcB\rFR6qZSfofTNCs7HI+VAIKV8CAwEAAQ==\r-----END PUBLIC KEY-----\r\n"
    },
    "invitation": {
      "encrypted_recipient_name": "Aes256Gcm.faHlODQ4Q3i11r6H-A8=.QUAAAAAFaXYADAAAAAD_GSnEC8ep0yiX7HYFYXQAEAAAAADUbHZ-1Bg6ZqmZkmo3t7JlAmFkAAUAAABub25lAAA="
    }
  }
EOF
# second part here actually makes the request
curl -X POST "https://sandbox.meeco.me/vault/invitations" \
  -d @data.json \
  -H "Accept: application/json" \
  -H "Meeco-Subscription-Key: Q7dFWQDGSsAE6tUo1G93Mlhn4SCOBrJe" \
  -H "Authorization: ANDJk1gJpFKmwrCjNyd7" \
  -H "Content-Type: application/json" \
  | json_pp \
  > invitation.json
```

The `invitation.json` file should have output that looks something like.

```json
{
   "invitation" : {
      "keypair_external_id" : "string",
      "token" : "cPWV4CwpWTHxhqaJGG9MRkVWxKojGhMPGs4oTuoBdTU",
      "message" : null,
      "user_image" : null,
      "integration_data" : {
         "organization_id" : "cc656956-6fb3-4bd1-a906-81c85dd6cb7e",
         "intent" : "service",
         "service_id" : "c213d93e-32a5-4e1d-96bb-0816e7eb6c74"
      },
      "id" : "62b54a86-66db-4e88-8e3d-3ce4457a2e97",
      "user_name" : null,
      "invited_user_id" : null,
      "outgoing" : true,
      "encrypted_recipient_name" : "Aes256Gcm.faHlODQ4Q3i11r6H-A8=.QUAAAAAFaXYADAAAAAD_GSnEC8ep0yiX7HYFYXQAEAAAAADUbHZ-1Bg6ZqmZkmo3t7JlAmFkAAUAAABub25lAAA=",
      "sent_at" : null
   }
}
```

The invitation has been created so lets make an unrelated user to accept this connection invitation.

```bash
meeco users:create -p supersecretpassword > .dave.yaml
```

Then for dave to accept this invitaion (and create the connection).

We should have a new keypair and `public_key` somewhere that we can use for this connection for Dave, dave might use the
meeco keystore to manage this but in this case lets forget about the keystore for now to keep it simple.  
  
Again the `keypair_external_id` would be a reference for Dave to retrieve the appropriate keypair (when using the keystore
this would be the id of the key record) but we can just put the name of the organization service in there for this reference.

The `invitation_token` here would be `.invitation.token` from the `invitation.json` file.  
  
The `Authorization` here should be Dave's authorization token (from `.dave.yaml`).

```bash
# first part here just writes the json to a file removing all whitespace and newlines
cat <<'EOF' |  tr -d '\n' | tr -s ' ' > data.json
  {
    "public_key": {
      "keypair_external_id": "DataSharingService",
      "public_key": "-----BEGIN PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAqMILz56KuEsGlsEpML3h\nPN7OUf7HrLTtWkazwbNIkK3RLWtNI5X8bd0ISUuwgXQVzZuWyC6wXPiWSDwXILAf\nJPusVT6Kh0EmXhJtBRCkF64fXKz54rI/2kYpuP4HZbNStj8OIRv/ECV7glDf+4Yq\nWDdLmh2vHDHF7bIRQnuWwV3ZL10Gkuh671aJkkVRdApUZUNcDfz1VK/SDMLxuxMg\n8D78u+d0WgThXzEwlcHtRitLA4QekNjVNPkoZoRIJSWLWqwkaOd0+YAhPNso3t9Y\nwaLT0/hDe4qBRuWpkoCIIUxE1JPuUbv3ePwsvyX9ksKNwQ6zVyOBXJwjspjac3mS\nH2DXS8dwU/ObKI3v9c/aY0i4iV6xJIkDxV2V8HclMOm1cLmgoc9YMU9YuM2LZ1GX\n707XBrdRD1v/FNQa/HJjFSapXcztN03etswrJcYFxVxDWZ84uMO2TNHOsSbOgop9\nmF5hRLQ+PT45v+mNNmbJa62yRaVkIV4VvsHgBVsz6TWsZGxu+IsNLKT/OYju/zzn\nvQSig5Ue7BAZqMOlibWiB6gX8vM336okrOWASnvWZNe+a7QpQdYYbHbH/leFZFCA\nht20DXUHLXYAKk9gvdqKycYnVsaRb2dnaSdUez+AUsIme0gWPscwSf3XpARUDc5i\nEKfIKQZzxyj4Y7W6eGwAfCMCAwEAAQ==\n-----END PUBLIC KEY-----\n"
    },
    "connection": {
      "encrypted_recipient_name": "Aes256Gcm.eDfbJAx9qhRZvjy1NnE=.QUAAAAAFaXYADAAAAAAPXFAVfuk4dKCl4VEFYXQAEAAAAACIVxZUFNld30Dy39K-DeZKAmFkAAUAAABub25lAAA=",
      "invitation_token": "cPWV4CwpWTHxhqaJGG9MRkVWxKojGhMPGs4oTuoBdTU"
    }
  }
EOF
# second part here actually makes the request
curl -X POST "https://sandbox.meeco.me/vault/connections" \
  -d @data.json \
  -H "Accept: application/json" \
  -H "Meeco-Subscription-Key: Q7dFWQDGSsAE6tUo1G93Mlhn4SCOBrJe" \
  -H "Authorization: hZwx4XPj6MZJcPHPKHqc" \
  -H "Content-Type: application/json" \
  | json_pp \
  > connection.json
```

The connection will have been created and the output in `connection.json` will look something like.

```json
{
   "connection" : {
      "own" : {
         "connection_type" : "service",
         "integration_data" : {
            "intent" : "service",
            "service_id" : "c213d93e-32a5-4e1d-96bb-0816e7eb6c74",
            "organization_id" : "cc656956-6fb3-4bd1-a906-81c85dd6cb7e"
         },
         "user_id" : "d62d37f8-9f53-4be2-9df3-af7541d08316",
         "id" : "c76b28f6-ab27-4b41-a8db-3184d17b8fbc",
         "user_public_key" : "-----BEGIN PUBLIC KEY----- ... -----END PUBLIC KEY-----\n",
         "user_type" : "human",
         "user_keypair_external_id" : "DataSharingService",
         "encrypted_recipient_name" : "Aes256Gcm.eDfbJAx9qhRZvjy1NnE=.QUAAAAAFaXYADAAAAAAPXFAVfuk4dKCl4VEFYXQAEAAAAACIVxZUFNld30Dy39K-DeZKAmFkAAUAAABub25lAAA="
      },
      "the_other_user" : {
         "connection_type" : "service",
         "id" : "359486bb-e998-4699-9eed-5e7194987565",
         "user_id" : "c3482646-f6cf-48a2-877c-04857802d4de",
         "integration_data" : {
            "organization_id" : "cc656956-6fb3-4bd1-a906-81c85dd6cb7e",
            "service_id" : "c213d93e-32a5-4e1d-96bb-0816e7eb6c74",
            "intent" : "service"
         },
         "user_keypair_external_id" : "string",
         "user_type" : "service_agent",
         "user_public_key" : "-----BEGIN PUBLIC KEY----- ... -----END PUBLIC KEY-----\n",
      }
   }
}

```

The `OrganizationService` has now made it's first connection with a user.
