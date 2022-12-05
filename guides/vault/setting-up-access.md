# Setting up Access

This guide contains step by step instructions on how to create user accounts for the _**Vault**_ and the _**Keystore**_.

![Create User with SRP](../.gitbook/assets/srp_create_user.svg)

* The _**Keystore**_ is where a users stores their secrets and encryption keys.
* The _**Vault**_ is where users store their encrypted user data. User data cannot be decrypted and read by anyone but the user.

Once the user has been authenticated a mobile/web application interacts with the _**Vault**_ and _**Keystore**_ to securely store user's data and encryption keys. The mobile application and/or its backend will perform calls to the _**Vault**_ and _**Keystore**_. Generation of various keys, data encryption, and decryption will also happen on the client side. In order to simplify the client side cryptography Meeco has developed an open-source library which implements the required cryptographic routines.

We've called it "Cryppo", and you can read about it and the languages it's available in on the [Cryppo page on this documentation site](../tools/cryppo.md)

Encryption of data and keys on the client side before it is sent up to the _**Keystore**_ or _**Vault**_ is an important part of the flow and infrastructure. By never sending unencrypted data to the services the layer of trust needed in a given service is greatly diminished and the likelihood of the data being decrypted by a nefarious entity should a data breach occur is extremely low.

For this guide, in order to emulate client behavior we will use the [Meeco-CLI](../tools/meeco-cli.md) to setup access to the _**Vault**_ and _**Keystore**_ and the [Cryppo-CLI](../tools/cryppo.md#cryppo-cli) to generate keys and encrypt/decrypt some data.

By the end of this guide, after reading and executing the scripts you will learn about which cryptographic keys exist for a user, and how they are encrypted and stored safely in the _**Keystore**_. The guide also demonstrate how keys are encrypted and decrypted.

### Setting up

Download the [Meeco-CLI](https://github.com/Meeco/cli) and the [Cryppo-CLI](../tools/cryppo.md#cryppo-cli) and follow the installation instructions for each tool. If you'd like to read more about the tools, you can find each description page in the [Tools](https://docs.meeco.me/tools) section on this site.

To explain what is required to access the Meeco _**Vault**_, we're going to look in detail at what the Meeco CLI does when you create a new user.

The CLI uses a Secret Key in combination with your user entered passphrase to generate both an encryption key and a password. The password is then used to authenticate using [Secure Remote Password protocol](https://en.wikipedia.org/wiki/Secure_Remote_Password_protocol) - otherwise known as SRP.

Below is a diagram that explains the flow.

![](../.gitbook/assets/hwo_the_secret_key_is_built.png)

### Creating a User with the Secret Key Flow

```bash
meeco users:create -p supersecretpassword > .user.yaml
```

The above command fires off a series of calls to the various Meeco APIs

_'Generating username'_

Firstly, we request a random username. It is a string that looks like `M9znjHbD5Xi`.

We also need to generate a random "Secret" that looks like the following: `xQCSCK-8A1d6d-emUjx8-YVGDcT-UhctWy-2rfHyv-7JNAgh-9` .

We combine the username and the secret, and prepend a version number to it. The resulting string is known as the "Secret Key" and takes the following form: `1.M9znjHbD5Xi.xQCSCK-8A1d6d-emUjx8-YVGDcT-UhctWy-2rfHyv-7JNAgh-9`

Now that we have our secret key, it's time to derive the credentials that we need in order to create our user.

We use the user's passphrase - `supersecretpassword` from the CLI command - and use the "Secret" as a salt to generate the "Passphrase Derived Key" \(PDK\). The PDK is later used to encrypt the "Key Encryption Key" \(KEK\). The passphrase derived key ends up looking like the following string: `24rdL_ugryujjDLcvwt00ARWWMCF4kk4gJETJy6yRjs=`

We also generate an "SRP Password" which uses the _reverse_ of the Secret Key as a salt.

We're now ready to create a _**Keystore**_ account.

## Creating A Keystore User Account

_'_'_Create SRP keystore user'_

The CLI uses the SRP Password and the SRP username in order to create a salt and a verifier, and sends this _plus_ the SRP username to the _**Keystore**_ API.

Now, the CLI is going to log into the _**Keystore**_ via SRP.

_'Requesting SRP challenge from server'_

From here, the server generates a challenge from that salt, and sends it back to the client \(in this case, the CLI\) to solve. Once the proof has been computed from the challenge,

_'Creating SRP session with proof'_

The server receives the solution and verifies it, which sends back a _**Keystore**_ authentication token. Login to the _**Keystore**_ is complete!

Now we can access the _**Keystore**_, generate keys to store in there, and request external admission tokens from it so that we can eventually access the Vault.

## Requesting Access To The Vault

_'Request External Admission Tokens from the Keystore'_

It is time for the CLI to get the required token so that it can get access to the _**Vault**_.

In order to restrict users from creating more than one user account in the _**Vault**_, the vault account is created using an admission token we request from the _**Keystore**_. The _**Keystore**_ will request an admission token from the _**Vault**_ on behalf of the user:

This is how the CLI does this behind the scenes:

```bash
curl -v -X GET "https://sandbox.meeco.me/keystore/external_admission_tokens"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY"
-H "Authorization: KEYSTORE_ACCESS_TOKEN"
```

The response contains the _**Vault**_ API admission token, which the CLI will use later to get access to the _**Vault**_

```javascript
  "external_admission_token": {
    "vault_api_admission_token": VAULT_API_ADMISSION_TOKEN,
    "passphrase_store_admission_token": null
  }
}
```

## Generating the _Key Encryption Key_ And Encrypting It

_'Generate and store key encryption key'_

The key encryption key \(_KEK_\) is a special encryption key which is used to encrypt all of the user’s other keys \(data encryption keys and keypairs\). There is only one _KEK_ per user.

The CLI uses the `cryppo` library to generate a new random key, and then encrypt and serializes it with the _PDK_ from the earlier secert key steps.

To do this manually, you can use the `cryppo-cli` library by using the following command with the unencrypted KEK you can find in the user's YAML file that the CLI creates in the root of the CLI directory:

```bash
➜ cryppo genkey

URL-Safe Base64 encoded key:
INUyR39qQcu43rqLeJtoTSMbzrB6NjlkSEujnM99ow4=

➜ cryppo encrypt -v INUyR39qQcu43rqLeJtoTSMbzrB6NjlkSEujnM99ow4= -k {PASSPHRASE DERIVED KEY}

Aes256Gcm.XKa_28KCjz_qUICv3XDi3x2SFSWCgVj3kkxvBg1QcfnG0Zkn7ooonHrnV48=.LS0tCml2OiAhYmluYXJ5IHwtCiAgek5Sa004M2lwRFYvd1hhegphdDogIWJpbmFyeSB8LQogIGpzb0pWbjBNNytLWWpoU3p0c2lpb1E9PQphZDogbm9uZQo=
```

The resulting encrypted _KEK_ looks like this: `Aes256Gcm.3EvCOUWD-zsHfe5hvrsMcppHx14SOMHXrEZC4Eyfw3s2JpvrIQRAH5Ydqcc=.LS0tCml2OiAhYmluYXJ5IHwtCiAgSVdjQ0M1ZC9FeHdRSVVFMAphdDogIWJpbmFyeSB8LQogIFN0L1o5QTZOMCswczBWQXgxcjQ0Rmc9PQphZDogbm9uZQo=`

If you look at the serialized encrypted _KEK_ you might notice it contains 3 parts concatenated with dots. This is the serialization format of `cryppo`. If no derived key is used, each such string contains 3 parts concatenated with a dot:

* Encryption strategy name
* Encrypted data encoded with Base64
* Encryption artefacts serialized into a hash converted to YAML, then encoded with Base64

#### Storing The Serialized Encrypted Key Encryption Key

The CLI now stores the serialized and encrypted _KEK_ in the keystore.

```bash
curl -v -X POST "https://sandbox.meeco.me/keystore/key_encryption_key"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY"
-H "Authorization: KEYSTORE_ACCESS_TOKEN"
--data-ascii "{
  ^"serialized_key_encryption_key^": ^"SERIALIZED_KEK_FROM_CRYPPO-CLI_OUTPUT"
}"
```

Response from the server:

```javascript
{
  "key_encryption_key": {
    "id": "4c43045d-7ff6-43b1-9700-53bf280f065a",
    "serialized_key_encryption_key": "SERIALIZED_KEK_FROM_CRYPPO-CLI_OUTPUT"
  }
}
```

## Generating the Data Encryption Key And Encrypting It

_'Generate and store data encryption key'_

_Data Encryption Keys_ \(DEKs\) are used to encrypt data. A user can have different data encryption keys used for different purposes.

For instance, you will have one for encrypting _**Vault**_ data, and one per _**Connection**_ that you create with a user.

To store a _DEK_ we need to encrypt it with the _Key Encryption Key_.

This follows the same general form as generating and encrypting the _KEK_, but this time, we encrypt the _DEK_ with the _KEK_ instead of the _PDK_.

```bash
➜ cryppo genkey
URL-Safe Base64 encoded key:
FOQSBUavGP23Fnrgo3mgIfYjk7bLMLpkWLVXEVwg9AU=

➜ cryppo encrypt -v FOQSBUavGP23Fnrgo3mgIfYjk7bLMLpkWLVXEVwg9AU= -k {KEY_ENCRYPTION KEY}
```

#### Storing The Encrypted Data Encryption Key

As with the _KEK_, we store the _DEK_ in the _**Keystore**_. The CLI uses the following API call:

```bash
curl -v -X POST "https://sandbox.meeco.me/keystore/data_encryption_keys"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY"
-H "Authorization: KEYSTORE_ACCESS_TOKEN"
--data-ascii "{
  ^"serialized_data_encryption_key^": ^"SERIALIZED_DATA_ENCRYPTION_KEY^"
}"
```

Response:

```javascript
{
  "data_encryption_key": {
    "id": "2a78322d-fe8a-4b69-af3d-bba3c66d0cd6",
    "serialized_data_encryption_key": "SERIALIZED_DATA_ENCRYPTION_KEY"
  }
}
```

## Generating a Keypair

_'Generate and store vault key pair'_

A user can have many keypairs for different purposes. A keypair can be tagged by the client to specify what the keypair is used for.

We will generate a keypair which we'll use for authentication into the _**Vault**_.

Keypairs are stored in the following fashion:

* Public keys are stored unencrypted
* Private keys are encrypted with the Key Encryption Key

The CLI will do the following:

1. Decrypt the encrypted KEK with the Passphrase Derived Key
2. Generate a RSA keypair with `cryppo`
3. Extract the public key
4. Encrypt the private key with the KEK

The `cryppo-cli` code that does the above looks like this:

```bash
➜ cryppo genkeypair -P pubKey -p privKey
Wrote new key pair

➜ cryppo encrypt  -v "-----BEGIN RSA PRIVATE KEY-----
{ ... }
-----END RSA PRIVATE KEY-----" -k {KEY ENCRYPTION KEY}
```

The CLI now has the encrypted Private Key ready to be uploaded to the _**Keystore**_

#### Storing The Keypair

Let's store the keypair. We will later use it for accessing the _**Vault**_, therefore we'll tag it with `vault`:

```bash
curl -v -X POST "https://sandbox.meeco.me/keystore/keypairs"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: acecb1c549ac4edb9deb4ea9c9ca8d01"
-H "Authorization: lVrriqUgXfX8yr7JT7p1kcZIi6O2UMVsQsnK-NYUc0I=.EFBPrsntOlltcA2g79bbtkhmYAWB9p9VQRfEm6GUZa4="
--data-ascii "{
  ^"encrypted_serialized_key^": ^"ENCRYPTED_SERIALIZED_PRIVATE_KEY^",
  ^"public_key^": ^"-----BEGIN PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA8ZEqPqHpVgWvK+NT0SR2\noVom2dv6QStVeYqgFnKm6TvZy66Gl8gQs6KjbgHc9dEfceN6yrE3WfvOwItgJSsY\nmsZVjuSyyXYtW3rVPhIEtsw/HaBcoC1HP3tuk7+YLq1UVjeOBtAt9yrhcGpkU2f4\nMi4Z7q1MVxErX2cdAbe+LLSx6Gx0n3hqPrmpMG6ZRoZ3dc4xNifs7K1RIt6hVSfy\nhIjgDr3Ret98CG+Vm5mJ3Vd5/BG0UE/j0NyKoGlqJg3VrJEVXcQoQ4DsUGj2GMu1\nj3DjyIn64ba4F2qpK4bJ6QcKGZsgr5K17KDguGBJ9B6HJ3PPd4oSSDHq7wfpu+bT\nNzN4NJ+eyIQJqYbwj1e18ETwHcogc2jpzGUHSEs1rTiUEibRFRUkqtlk4bXar/tz\n0II7bV9hIlhOaf9urqDwCtkN34p/h36eDqDxCaDeg4QtjBuZu9IgizrxHQZD5j8H\ntAhkULG6NqrAAynRxBDM+0kmZr+uzEL155YPnezIAoeCj8NlwkcUCm2ZCcQkA3AK\nvrkqGEOAm9QYqpyjpujjDDVgQNZXiQnWg94DG6CnRFqyFRPOfVKyhmcziqL1ejfV\ngPyrIHiU/5dmFu5LbmDjaUIX6j3DvYbXy87dPxsOESmeQWFQGY3REN+ZSwS+3FAP\n1aTS4Bwim9RiPycM3MY2vTUCAwEAAQ==\n-----END PUBLIC KEY-----\n",^",
  ^"metadata^": {},
  ^"external_identifiers^": [
    ^"vault^"
  ]
}"
```

Response:

```javascript
{
  "keypair": {
    "id": "8b261bdc-2526-4a7f-b403-61f357a847c2",
    "public_key": "-----BEGIN PUBLIC KEY-----\nPUB_KEY_CHARACTERS\n-----END PUBLIC KEY-----\n",
    "encrypted_serialized_key": "Aes256Gcm.REST_OF_CHARACTERS",
    "metadata": {},
    "external_identifiers": [
      {
        "id": "83357673-92fd-4928-bef3-741352af6f62",
        "identifier": "vault",
        "keypair_id": "1320c3fb-8a58-411c-9c76-ba13b3e4def7"
      }
    ]
  }
}
```

## Creating a Vault User Account

_'Create Vault API User'_

Here, the CLI is using the admission token that it got from the 'Request External Admission Tokens from Keystore' step earlier on.

Importantly, this call is made to the _**Vault**_ API instead of the _**Keystore**_ API

An _admission token_ can only be used once. You can think of it like a ticket for a plane ride - once you're in, you can't use it again! To create a vault user account we'll submit:

* The _admission token_
* The _public key_ from the keypair we generated.

```bash
curl -v -X POST "https://sandbox.meeco.me/vault/me"
-H "Content-Type: application/json"
-H "Cache-Control: no-cache"
-H "Meeco-Subscription-Key: DEV_PORTAL_SUBSCRIPTION_KEY"
--data-ascii "{
  ^"public_key^": ^"-----BEGIN PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA8ZEqPqHpVgWvK+NT0SR2\noVom2dv6QStVeYqgFnKm6TvZy66Gl8gQs6KjbgHc9dEfceN6yrE3WfvOwItgJSsY\nmsZVjuSyyXYtW3rVPhIEtsw/HaBcoC1HP3tuk7+YLq1UVjeOBtAt9yrhcGpkU2f4\nMi4Z7q1MVxErX2cdAbe+LLSx6Gx0n3hqPrmpMG6ZRoZ3dc4xNifs7K1RIt6hVSfy\nhIjgDr3Ret98CG+Vm5mJ3Vd5/BG0UE/j0NyKoGlqJg3VrJEVXcQoQ4DsUGj2GMu1\nj3DjyIn64ba4F2qpK4bJ6QcKGZsgr5K17KDguGBJ9B6HJ3PPd4oSSDHq7wfpu+bT\nNzN4NJ+eyIQJqYbwj1e18ETwHcogc2jpzGUHSEs1rTiUEibRFRUkqtlk4bXar/tz\n0II7bV9hIlhOaf9urqDwCtkN34p/h36eDqDxCaDeg4QtjBuZu9IgizrxHQZD5j8H\ntAhkULG6NqrAAynRxBDM+0kmZr+uzEL155YPnezIAoeCj8NlwkcUCm2ZCcQkA3AK\nvrkqGEOAm9QYqpyjpujjDDVgQNZXiQnWg94DG6CnRFqyFRPOfVKyhmcziqL1ejfV\ngPyrIHiU/5dmFu5LbmDjaUIX6j3DvYbXy87dPxsOESmeQWFQGY3REN+ZSwS+3FAP\n1aTS4Bwim9RiPycM3MY2vTUCAwEAAQ==\n-----END PUBLIC KEY-----\n^",
  ^"admission_token^": ^"2571b0b6c580a7785c8b^"
}"
```

The response is as follows:

```text
{
  "encrypted_session_authentication_string": "string",
  "user": {
    "id": "string",
    "private_encryption_space_id": "string",
  }
```

The response contains

* ID of the user in the _**Vault**_
* an encrypted session token

To decrypt the encrypted session authentication token, the CLI feeds the private key and the encrypted session authentication token into `cryppo`.

The Cryppo-CLI can do this for you with the following command.

```bash
➜ cryppo decrypt -s {encrypted_session_authentication_string} --privateKeyFile {PRIVATE KEY FILE FROM EARLIER KEYPAR GENERATION TO FILE}
```

The output is then used in the next step for creation the encryption space for the new User.

_'Update Vault Encryption Space'_

The final job for the CLI is to create a new encryption space for the user. This is a DEK identifier.

## Logging In Into The Vault

Once decrypted, encrypted session token can now be used with the `Authorization` header to work with the _**Vault**_.

## Next Steps

The Login flow has been completed, and now you can use the token to try out the API calls in the _**Vault**_ at the [Meeco Developer Portal playground for the Vault](https://dev.meeco.me/api-details#api=meeco-vault-api)

If you created a user in the CLI, you can use the tokens and keys in the metadata section of your `.user.yaml` files to try out more API requests.

