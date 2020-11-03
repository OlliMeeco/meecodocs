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

```


2. Retrive organization credential
Ensure organization is created and validated first (see above)
You can retrive organization credential using follwoing command meeco organizations:login -o .my-created-organization.yaml > .my-org-login.yaml