# Setting up Access

This guide contains step by step instructions on how to create user accounts for the _**Vault**_ and the _**Keystore**_.

* The _**Keystore**_ is where a users stores their secrets and encryption keys.
* The _**Vault**_ is where users store their encrypted user data. User data cannot be decrypted and read by anyone but the user.

In this scenario the means of creating and authenticating a user is done by an external party's infrastructure. This allows the _**Vault**_ and _**Keystore**_ to be provided to the external party's existing userbase.

Once the user has been authenticated a mobile/web application interacts with the _**Vault**_ and _**Keystore**_ to securely store users data and encryption keys. The mobile application and/or its backend will perform calls to the _**Vault**_ and _**Keystore**_. Generation of various keys, data encryption, and decryption will also happen on the client side. In order to simplify the client side cryptography Meeco has developed an open-source library which implements the required cryptographic routines. Currently there are two ports of this library:

* Ruby [https://github.com/Meeco/cryppo](https://github.com/Meeco/cryppo)
* Javascript \(Typescript\) [https://github.com/Meeco/cryppo-js](https://github.com/Meeco/cryppo-js)

Encrypting of data and keys on the client side before it is sent up to the _**Keystore**_ or _**Vault**_ is an important part of the flow and infrastructure. By never sending unencrypted data to the services the layer of trust needed in a given service is greatly diminished and the likelihood of the data being decrypted by a nefarious entity should a data breach occur is extremely low.

For this guide, in order to emulate client behaviour we will use short snippets of Ruby code. You can also find executable scripts in directory `scripts`. Please make sure you are running `Ruby 2.5` or higher. You can find installation instruction for Ruby here: [https://www.ruby-lang.org/en/documentation/installation/](https://www.ruby-lang.org/en/documentation/installation/) .

By the end of this guide, after reading and executing the scripts you will learn about which cryptographic keys exist for each user, and how they are encrypted and stored safely in the _**Keystore**_. The scripts also demonstrate how keys are encrypted and decrypted.

## 00. Setting Up

### Subscription Keys

Firstly, a developer must sign up at the [Meeco Developer Portal](http://dev.meeco.me/).

After following the signup procedure, confirming your account via an email and finally logging in, select "Products" from the header bar.

Select a product, give your new subscription a name, then hit "Subscribe".

This will generate the Primary and Secondary keys that are required to make requests in the developer portal.

The Primary key will be added to the `Meeco-Subscription-Key` header in all requests to the portal.

Once the subscription keys have been activated, you can begin to use them to make requests.

### Cryppo

### Recommended setup with Docker

if you have Docker installed on your system, you can type `docker-compose run cli /bin/bash` in the Ruby Cryppo root directory. This will give you a container with the required version of Ruby and bundler, and open a bash shell there where you can test the requests.

Let's check if Cryppo is ready to be used. Change directory to the directory with scripts and execute the following command:

```bash
./00.check_cryppo.rb

Cryppo found and is usable
```

### Setup without Docker

The Ruby port of Cryppo exists as a repository on Github and has not been published as a package yet. In order to use it we need to install Bundler first.

On a POSIX system like MacOS or Linux open a shell and run the following command:

```bash
gem install bundler -v '1.17.3'
```

Let's check if Cryppo is ready to be used. Change directory to the directory with scripts and execute the following command:

```bash
./00.check_cryppo.rb

Cryppo found and is usable
```

## 01. Creating A Keystore User Account

Setting up user accounts begins with the _**Keystore**_.

We will be creating cryptographic keys. By the end of the guide we will have the following hierarchy of the keys:

To access the keystore, firstly a user needs to create an `admission_token`. This is created with a call to the `/admission_tokens` endpoint. In order to perform this call the external party must possess a `provider_api_key`. A `provider_api_key` is a way to restrict calls to trusted parties only. A `provider_api_key` should be put in the `Authorization` header. This call has no body.

```bash
curl --request POST \
  --url https://meeco-api-dev.azure-api.net/keystore/admission_tokens \
  -H 'accept: application/json' \
  -H 'authorization: PROVIDER_API_KEY' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY'
```

The result will contain the `admission_token` object that contains, the `id`, `token_value`, and whether the admission token has been `used`.

```javascript
{
  "admission_token": {
    "id": "0de1617f-de0a-4e8e-9f8c-85c008901354",
    "token_value": "1e36372e6e6c5d8c00cd",
    "used": false
  }
}
```

Create a user by hitting `POST /users`.

The body of this call requires three things:

1. The `admission_token` from the previous call's response
2. A `username`
3. A `passphrase`

```bash
curl --request POST \
  --url https://meeco-api-dev.azure-api.net/keystore/users \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  --data '{
    "admission_token": "1e36372e6e6c5d8c00cd",
    "username": "Run-Through",
    "passphrase": "password"
}'
```

The response will contain a `user` object with an `id` and the `username`

```javascript
{
  "user": {
    "id": "0402ebb0-ddf0-452e-89c1-00a7408d200b",
    "username": "Run-Through"
  }
}
```

## 02. Logging in to the Keystore

Next, a user will need to create a _Session_ with `POST /session`

```bash
curl --request POST \
  --url https://meeco-api-dev.azure-api.net/keystore/session \
  -H 'content-type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "username": "Run-Through",
    "passphrase": "password"
}'
```

The result returns the following:

```javascript
{
  "session": {
    "session_authentication_string": "SESSION_AUTHENTICATION_STRING",
    "id": "77d46dd8-fda6-4e4a-918c-475654010db5",
    "valid_from": "2020-01-29T05:40:54.624Z",
    "valid_till": "3020-01-29T05:40:54.624Z",
    "username": "Run-Through"
  }
}
```

From now on all calls are going to contain `session_authentication_string` in the `Authorization` header. Each session authentication string has a limited lifetime. Once it expires, a new session token can be requested in the same fashion.

## 03. Generation Of The Password Derived Key

The first step in generating cryptographic keys is generation of the _password derived key_ \(PDK\). The PDK itself is not stored in the _**Keystore**_, but it can be re-generated from a password and _key derivation artefacts_. Key derivation artefacts will later be stored in the Keystore.

Change directory to the directory with scripts and execute the following command:

```bash
./03.generate_password_derived_key.rb mycoolpassphrase

derivation artefacts:
{"salt"=>"\x0E\xD0\b\xF7MK\x9D\xEA\xAC\xD4\x1E\xF5z\xA7d\x9C\xD7a\\d", "iter"=>21129, "length"=>32, "hash"=>"SHA256"}

derivation artefacts base64:
"eyJzYWx0Ij0-Ilx4MEVceEQwXGJceEY3TUtceDlEXHhFQVx4QUNceEQ0XHgxRVx4RjV6XHhBN2RceDlDXHhEN2FcXGQiLCAiaXRlciI9PjIxMTI5LCAibGVuZ3RoIj0-MzIsICJoYXNoIj0-IlNIQTI1NiJ9"
```

If you peek into the script, here are the lines that do the work:

```ruby
derivation_strategy = Cryppo::KeyDerivationStrategies::Pbkdf2Hmac.new
passphrase_derived_key = derivation_strategy.generate_derived_key(passphrase)
derivation_artefacts = stringify_keys(passphrase_derived_key.derivation_artefacts)
```

## 04. Storing The Password Derivation Artefacts

Now we need to store the key derivation artefacts from the previous step in the _**Keystore**_ :

Execute the following command:

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/keystore/passphrase_derivation_artefact \
  -H 'Authorization: SESSION_AUTHENTICATION_STRING' \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "derivation_artefacts": "DERIVATION_ARTEFACT_BASE_64",
    "verification_artefacts": ""
}'
```

The response will be a 200 with the same artefacts:

```javascript
{
  "passphrase_derivation_artefact": {
    "id": "b63df0f2-83f4-4a53-8215-c4e0941422c9",
    "derivation_artefacts": "DERIVATION_ARTEFACT_BASE_64",
    "verification_artefacts": ""
  }
}
```

## 05. Generating the _Key Encryption Key_ And Encrypting It

_Key Encryption Key_ is a _key encryption key_ \(KEK\) which will be used to encrypt all other keys \(data encryption keys and keypairs\). There is only one _KEK_ per user.

To store the _Key Encryption Key_ we will first encrypt it with the PDK \(see step 03\).

We will submit the following arguments to script `05.generate_the_kek_and_encrypt_with_pdk.rb`:

* The passphrase
* derivation artefacts in their base64 format returned from step 03

For the sake of simplicity you can just copy the derivation artefacts from step 03, but in the real scenario they are read from the _**Keystore**_ with `GET /passphrase_derivation_artefact`.

In order to re-generate the PDK we will need combine the passphrase and the derivation artefacts \(in their base64 format from step 03\):

```bash
./05.generate_the_kek_and_encrypt_with_pdk.rb mycoolpassphrase "eyJzYWx0Ij0-Ilx4RTJceDlGalx4OUFceDk5LFx4QTRceDhBXHhDMX1ceEFFXHhGM1x4QTVRXHhERlx4QzQkXHhEMVx4MTAkIiwgIml0ZXIiPT4yMDQ0MywgImxlbmd0aCI9PjMyLCAiaGFzaCI9PiJTSEEyNTYifQ=="

restoring the password derived key from the password and derivation artefacts... [done]

serialized encrypted key encryption key:
"Aes256Gcm.SERIALIZED_KEK.LS0tCml2OiAhYmluYXJ5IHwtCiAgbEV2c0p3cGp2eWw0a1NuegphdDogIWJpbmFyeSB8LQogIFlkU1YxYjIzbGJiK3ZJZXZBaVUrZ1E9PQphZDogbm9uZQo="
```

The main steps in this script are

1. Re-generate the Password Derived Key \(PDK\) from the passphrase and the derivation artefacts
2. Generate the Key Encryption Key \(KEK\)
3. Encrypt the KEK with the PDK

Here are steps 2 and 3 from the script:

```ruby
# generate the KEK
encryption_strategy = Cryppo::EncryptionStrategies::Aes256Gcm.new
key_encryption_key = encryption_strategy.generate_key

# encrypt the KEK with the PDK
encrypted_key_encryption_key = Cryppo.encrypt(
  encryption_strategy.strategy_name,
  pdk,
  key_encryption_key.unwrap_key
)
serialized_encrypted_kek = encrypted_key_encryption_key.serialize
```

If you look at the serialized encrypted KEK you might notice it contains 3 parts concatenated with dots. This is the serialization format of Cryppo. If no derived key is used, each such string contains 3 parts concatenated with a dot:

* Encryption strategy name
* Encrypted data encoded with Base64
* Encryption artefacts serialized into a hash converted to YAML, then encoded with Base64

## 06. Storing The Serialized Encrypted Key Encryption Key

Let's store the encrypted _**Key Encryption Key**_ in the _**Keystore**_:

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/keystore/key_encryption_key \
  -H 'Authorization: SESSION_AUTHENTICATION_STRING' \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "serialized_key_encryption_key": "Aes256Gcm.SERIALIZED_KEK.LS0tCml2OiAhYmluYXJ5IHwtCiAgZVRHM1BuelQ5RWt1cVNtegphdDogIWJpbmFyeSB8LQogIGREbnlxdVVzNk8rUVZSVzdkZmpId0E9PQphZDogbm9uZQo="
  }'
```

```javascript
{
  "key_encryption_key": {
    "id": "4c43045d-7ff6-43b1-9700-53bf280f065a",
    "serialized_key_encryption_key": "Aes256Gcm.SERIALIZED_KEK.LS0tCml2OiAhYmluYXJ5IHwtCiAgZVRHM1BuelQ5RWt1cVNtegphdDogIWJpbmFyeSB8LQogIGREbnlxdVVzNk8rUVZSVzdkZmpId0E9PQphZDogbm9uZQo="
  }
}
```

## 07. Generating the Data Encryption Key And Encrypting It

_Data Encryption Keys_ are used to encrypt data. A user can have different data encryption keys used for different purposes.

To store a data encryption key we need to encrypt it with the _Key Encryption Key_ from step 05.

We will provide the following the following arguments to the script:

* the passphrase from step 03
* the derivation artefacts in their base64 format from step 03
* the encrypted master key from step 05

In a real life scenario, the derivation artefacts and the encrypted Key Encryption Key are read from the _**Keystore**_, but in this guide we are simplifying this by copy-pasting the values from the previous steps.

The script will do the following:

1. Re-generate the password derived key from the passphrase and the derivation artefacts
2. Decrypt the encrypted master key with the password derived key
3. Generate a Data Encryption Key
4. Encrypt the Data Encryption Key with the Key Encryption Key

```bash
./07.encrypt_the_dek_with_the_kek.rb mycoolpassphrase \
  "eyJzYWx0Ij0-Ilx4RTJceDlGalx4OUFceDk5LFx4QTRceDhBXHhDMX1ceEFFXHhGM1x4QTVRXHhERlx4QzQkXHhEMVx4MTAkIiwgIml0ZXIiPT4yMDQ0MywgImxlbmd0aCI9PjMyLCAiaGFzaCI9PiJTSEEyNTYifQ==" \
  "Aes256Gcm.i4shGUkjplVTLNeLCvZQSFhOc6drJbDUGEp-op9mvZ8=.LS0tCml2OiAhYmluYXJ5IHwtCiAgZVRHM1BuelQ5RWt1cVNtegphdDogIWJpbmFyeSB8LQogIGREbnlxdVVzNk8rUVZSVzdkZmpId0E9PQphZDogbm9uZQo="

restoring the password derived key from the password and derivation artefacts... [done]

decrypting the master key (a.k.a. key decryption key)... [done]

serialized encrypted data encryption key:
"Aes256Gcm.ZMZgM-7-paIA0RG1mrG5eJ7S0iAzHhFXI1cEAhIFC8M=.LS0tCml2OiAhYmluYXJ5IHwtCiAgUDZXb1R5bGhjU1JYNDZ3ZQphdDogIWJpbmFyeSB8LQogIEhYa3NoamtqZVQrbm1vUm9KenU1dFE9PQphZDogbm9uZQo="
```

The most important code in the script is as follows:

```ruby
# generate the DEK
encryption_strategy = Cryppo::EncryptionStrategies::Aes256Gcm.new
data_encryption_key = encryption_strategy.generate_key

# encrypt the DEK
encrypted_data_encryption_key = Cryppo.encrypt(
  encryption_strategy.strategy_name,
  kek,
  data_encryption_key.unwrap_key
)
serialized_encrypted_data_encryption_key = encrypted_data_encryption_key.serialize
```

## 08. Storing The Encrypted Data Encryption Key

Let's store the generated and encrypted data encryption key:

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/keystore/data_encryption_keys \
  -H 'Authorization: SESSION_AUTHENTICATION_STRING' \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "serialized_data_encryption_key": "Aes256Gcm.SERIALIZED_DATA_ENCRYPTION_KEY.LS0tCml2OiAhYmluYXJ5IHwtCiAgYXIyVW5aeDFuT0ZUTmFmVgphdDogIWJpbmFyeSB8LQogIEpGc1lqOUdXM2dvckorcjhNL0dLRHc9PQphZDogbm9uZQo="
  }'
```

Response:

```javascript
{
  "data_encryption_key": {
    "id": "2a78322d-fe8a-4b69-af3d-bba3c66d0cd6",
    "serialized_data_encryption_key": "Aes256Gcm.SERIALIZED_DATA_ENCRYPTION_KEY.LS0tCml2OiAhYmluYXJ5IHwtCiAgYXIyVW5aeDFuT0ZUTmFmVgphdDogIWJpbmFyeSB8LQogIEpGc1lqOUdXM2dvckorcjhNL0dLRHc9PQphZDogbm9uZQo="
  }
}
```

## 09. Generating a Keypair

A user can have many keypairs for different purposes. A keypair can be tagged by the client to specify what the keypair is used for.

We will generate a keypair which we'll use for authentication into the _**Vault**_.

Keypairs are stored in the following fashion:

* Public keys are stored unencrypted
* Private keys are encrypted with the Key Encryption Key

We will provide the following arguments to the script:

* the passphrase from step 03
* the derivation artefacts in their base64 format from step 03
* the encrypted KEK from step 05

The script will do the following:

1. Re-generate the password derived key from the passphrase and the derivation artefacts
2. Decrypt the encrypted KEK with the password derived key
3. Generate a keypair
4. Extract the public key
5. Encrypt the private key with the DEK

```bash
./09.generate_the_keypair_and_encrypt_the_private_key_with_the_kek.rb mycoolpassphrase "eyJzYWx0Ij0-Ilx4RTJceDlGalx4OUFceDk5LFx4QTRceDhBXHhDMX1ceEFFXHhGM1x4QTVRXHhERlx4QzQkXHhEMVx4MTAkIiwgIml0ZXIiPT4yMDQ0MywgImxlbmd0aCI9PjMyLCAiaGFzaCI9PiJTSEEyNTYifQ==" "Aes256Gcm.i4shGUkjplVTLNeLCvZQSFhOc6drJbDUGEp-op9mvZ8=.LS0tCml2OiAhYmluYXJ5IHwtCiAgZVRHM1BuelQ5RWt1cVNtegphdDogIWJpbmFyeSB8LQogIGREbnlxdVVzNk8rUVZSVzdkZmpId0E9PQphZDogbm9uZQo="

restoring the password derived key from the password and derivation artefacts...  [done]

decrypting the master key (a.k.a. key decryption key)...  [done]

private key:
"-----BEGIN RSA PRIVATE KEY-----\nPRIVATE_KEY_CHARACTERS\n-----END RSA PRIVATE KEY-----\n"

serialized encrypted keypair:
"Aes256Gcm.MZQD65xuK3RTTBJ9GdcaV1Sv7yztXkl4JYXcn4yUAHexsnZAAJop5yIIvENicLuWQxng9ZjAwaaAlfEbIUAyyPfkmDkHUWlxItAUAWEdvr9EbJtUKrsCj4IP8lSKttV49LlowV-22j1zabiTxLWnGF_U1hrxqFSG2wtDX34upxvr2pZ-30z4LcYLUjFpW4Axbl5m2jbDM05jE64ND6uqG3xyUJNvXbgKpj_glSl4djJdya82ngRDTZpUE2j_QvQOakkahWXbC2jYnC6EcIS4tTgi5XUEjWY-R520rWtDUsJDCSwNbBtoi7asJOjodAHv5wCDK8ppGcEnK1ONE-e_YluBM9tcujGh9MF0cW2ntM_aW41w5rKNgRr7hsbaD-3-bf1QpZZYcxov5nlAjAAAHYsucEW2xFJUC0-yLjVZJx-mjtYzxKTbDeZF4ZXiyk-UzAt4I0zzS6lgmW2SWG6RhMLm1rMKI3hOjAJZbUk-wBMAKLidTCmz3S7YZWmqa9FylLLEGbbG3U5WGJ9ApJhDiqok0MEUAY2hA3oV56768MGHqM9qQGtJiBVJc9P2YroFn_ApfdkP75aZRWpka8xAmFn-BsZfhuPRoLTWtoy_Bm0OyOdUgYeXcGz3D3CmmB-M8s-jY3QoPQRjHtrfMIdoKMTOVxwm57OTjRSUFHmsFmxvOzK4dRh49uvrbieMhtkBZVOTb_N6ra4__V2JzTNfucPfNT2ncc9AyKRn7QxQpRR-4spyo-WzINtNAlT47ApffnSqOEQ6ICxvlmSDvE3c53ixqdw8dVoX9MqgCn8i3jZYsY2cfYYplCJSnHnN3hmSlbmAH3LFLSemsT0yZINOWFeni9cn1dTh3KSbmX0bqOKsKVoNPfycmgftk5CRbctMFHTferHwlrOd36emr9VvWO5-Qg88ToRbCBXoAAW7XAxHk0g91AGeBFxCMc490d1mC--fMiEixoMZvAUzhOMaVb9s-UI6LJGIVvmjxn1yako2fvg8gCoAdaWJ4UVeRyHtFAWd7MhBWINXQB2G78F7bcOgSlYtWt94L4LkalSaMWDxwyzlXg7dowvBiHslMKRuAye2cHojuK35IeZ5ml8NOsCLekD2hQzOw6OxH7Zd9MaZWnJblTk-Rm2Ezn-h8xBQJu609b7iCnn7w7JljiVrKKha6W3sOWXfyUoOFFo1EuyNT-noMEbO1hlLx2izmMcgaHfusu2ipxIu36WwFvVbp27Iic1j1XTLubWQGPOzkyoihbBOuKGax895NKkqtv590Oe-iqsFptyPgTSFHDpd5x4J_XOjb-c02E3m6i7kicJVtyiFSrLkqk1sOZPvOiABHsao2bmVN_sweDsOLo-v-cjOAS0do6nZprfS5EtNqMVoWfWVgT5uBlu-IJlPPuGz4PpQOYJK7FVW5cQ1ioCA16OMwBFGMy1Tn3M-E4NvSLF0NOJ5CjWnlZW2kkMB2KurUjVhAwI6EE0Cq1G_ZbIy2gv1eNjiqfUo3ShrlaEiSlJsc63PeEYwKrX2lXtZbQYoKiE2eOmjXnCSf7lw99dIoaO_fONqlPdkQYtwn-DE6n_6q6ItH3HadfJh7FlSNjOY1TDD1V3j5BJaG4sbKGPyBr0xjJzcEEoznkihBB0-FkRXX98kE63U8osF1K1SqWg9eG8Kja2XSkUGdJOZy4qEF8V6cIaJLBAmRzQSIPKF3ukgIHWd8ZPbOCkBctNPo6n1di7_ZsAejWO7zgRSgpZl0JR2Uj31HtqsxZt4vBYe3c-ac_Xa8mNr3wnImNPNhq28gpwYO2sB6hSvZ9sIfdmpOrbAqJozo45IyFeTNVDzeqs4HSgJ5VVkE7_vvF5tT-loxJ42aI84I1d76pzoP5VqC4OLlpPqRM-p_uMwHvhst-3fl6Zrwst-N8V2WSuka8paP_zfUHA_7R8jluder5N1pDUi-KXyXBYQR6BMtrxbiry64QCs258x3EBwJMZ7PSOxVcTsjrZCHAjYyS-e_NU5TNEWx7dp3EDPZPXLW9GUmzAZdmQtcxLjiXKiGoNDY_eAYAJOQ7vIOho-EljL1nUPyld6c64snTbTwx6dI2bySQ-6MXRUnPz1F3HRctxwyjsTXfHeI4rK1b9sVZln_Y6U0gU7YZ-ISsM4EINz1iH3aTYHpAF2IQvcO1tOtmPEhmfIm-F2LUATJi1BEhJWsFuZXCBDuCh9sBSjkWjbywj7jDT6WFjoqsnDRmTmjPKc39asPC-Oz4z4i4OhTY8yWjLIn_AB0MjgML0b3UJtQBqDZ40VOazV1IeirdHe2ZJQCnCsYL01bVCC-X0W0dxH7q9Ihx7iXOlLBl2KQyUXhjVukr2VAwGvvSXs8cIhV6V1V-BYm-EgWEgnZIS5agvBm65JqFr-jz7IoXftH8wiWC2F1SdDL5bO96FvsvcDNcR0v4kfg4J3XG6st0gXf6Qh3DkpPB90jWuK9VD_oEq861GiP8fjRWgzTin-e3Oz4jr1P4qoWyb1Wxbm6V8r_QgH-IBubiflrTSXV9D0lOanku8zOKcLj-bhMwg6k0yIknU4kiNAySCONIkEst32tgSMa4_Q2cb3v1ygVv5zwxETUPwINGtpj7ON9bRTeoGlAX2QkINfcJDYdm74RxuVBRKr4WR-d2VvaWAGLEZTJhLKNgTPbJevPay16rSuLJJzKKrKIdFLkJ4TzYkKQinHoe5sDk-zOcKMLR1qzk7S6a2HW8l4YDa_yJxgsNlbPs5RI0WBaFBAsMXFaBR5wcBgxj9RmZgBtsWqhrcpjz-gP4cylTThpE_YAfLEGEQklHmZ6LQ0jSf6D4_GlyDHM8k9EAqiFhrGvRnKfV6z3mWuxRoPoyaeFfIxHgvCcKf7_MqyTIVIlUuMPl92bO1vDpkNAnFbsCGe82dObHVhoH3gWXV_BeETT0ceocOvv0YEw_D8kGfscH3H7ZY4j-E4plWdNF-6WBYc7YKm6TAuqS4cATFLz2Syhm1aGbtQpW07hFScsSIBsrh9xBqgF7i66j47kO33Uta-dn9bgN9wZzm9-l7a5Lpg4N9nYVu0QRsg307HsLJIa5DyeraI_aIGRggrGGoqOFfx-Ufgb0RMB-v5uH5_ns0oIW0iOKN6Rf8PtUDlFCM4IvJ6pgOvbdqAikxEaarShr42I0WTulFKmO6ssZeqhrkAnNktLx1_aHSV7TLJrHZgVCTn7TnF_I_NEWDSejm0UqsZXjdDD-7lOndmxpB8dJq0Emp2Jw6GwESjL00kUcGJo_qLzjKSagxgJsnoTRlpjMrqFziI5YqZeE_MpznWqtnnjjAvpA2wFsmY_X1suOy_jINDFqDbDhtSAmf7eC1fw1Iif1cRVddzGvMQQ5-oy4bGZVTZgordPcYutOFqqrBEoD_8unDmV_c_Se4to-RFrJPKQIt1IPNabCG9En6fsm2VTeYn6tIQeXXOoN1HpJWqesIOGYQR3OJpH3vlnitVsAQEgTJWVYyEFdOXn2XYVuTx23OXkHH5a1-TKpynJDZwcEipGM7V1PcmM175cdMzLyTC5J6rukR_S6P-GkqY5NkA-_AprDwaWKTLLmVLlGzdldEvhDWBcUcC6Yb1r3aPP88DCPB7VjImQ41VlojaZxrTXKnQTPrJKGvxXQvob6OMCuGS5kALpDAIez9S-d2F47pLDi9zPZFjw8cC6S3jcZT5a4h8m2r26PqhjxhVLyx4PDXMkbrdASPw5ucOwyswipS-DMvkUAJltkd80JB2XjLwUXy-y5VAxplVyDJawjp0xlUYF8tLT20Bz2rsJEhz3ZOQCK3te99EdHAt5vKTacoviXDIMad9aPNuH9LMFInU4_YJxYkJfxanK_tOx3an7RGBBvQiWo2UUKkmouu6I1Zh8kcvOBPmy6tlNl8TWFccVThVytTEjOaopVw1-HGKA0XgvSNI22XFnXcIboPTy0dbmUZihiTxXOy2dQP3-tfvA3PPEp9A7ZLxwpuzj9JOC7eofGVC66puQm0nBSlQzS-bNUxmCSiAWJ8pIb9y8NeayBVcrxx9_kIbO5qsyHg3TROf6wVQqZDh7-TAwwhnoykc1c1aZ6BrVMhaPoUvtQNl_QLmCjfQ5AV9iEhcd-xhKB1ZM-oGrLhwOEoH82qOWMt-pkTbRHoFLjn2b3rF0V7dHpT8xbjTrQajUi08PEPWFGCudln5jueonn1E9maDFbt8LRbGT3xX7296dvjoAYRcvNQSlNaxCZrfuh1itdPqs-HMJM7NwpTZ1Unilep3weQBZMEoSu5iAoiZFUAKSSxp63UTh9qBoBRqCPHqRR_WqSMWVppOI5La0CFcXKV1.LS0tCml2OiAhYmluYXJ5IHwtCiAgN3o4SkthbGVkcjNGVm1mTwphdDogIWJpbmFyeSB8LQogIEVJaWRCUFdlejQxb0lnaUdFcXo0NkE9PQphZDogbm9uZQo="

public key:
"-----BEGIN PUBLIC KEY-----\nPUB_KEY_CHARACTERS\n-----END PUBLIC KEY-----\n"
```

The returned values are:

* _Unencrypted Keypair_. We will not store it in the _**Keystore**_, the only reason we are printing it is to avoid too much complexity in step 13
* _Serialized Encrypted Keypair_ to be stored
* _Public Key_ to be stored

Here is the code that does the heavy lifting:

```ruby
# generate a keypair
rsa_encryption_strategy = Cryppo::EncryptionStrategies::Rsa4096.new
keypair = rsa_encryption_strategy.generate_key

# convert the keypair to PEM
private_key_pem = keypair.unwrap_key.to_pem

# encrypt the private key PEM with the master key (key encryption key)
encryption_strategy = Cryppo::EncryptionStrategies::Aes256Gcm.new()
encrypted_private_key_pem = Cryppo.encrypt(
  encryption_strategy.strategy_name,
  kek,
  private_key_pem
)

# serialize the encrypted private key to the Cryppo serialization format
serialized_encrypted_keypair = encrypted_private_key_pem.serialize

# get the public key
public_key = keypair.unwrap_key.public_key.to_s
```

## 10. Storing The Keypair

Let's store the keypair. We will later use it for accessing the _**Vault**_, therefore we'll tag it with `vault`:

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/keystore/keypairs \
  -H 'Authorization: W4cHLuLVmrmfR6C8aYS3kTCTcHBoxns7csY-Kmcv0lM=.tnmxZbKntbd8Vy_2gT3c4URVkVMbYj3XV-ioy_2VU90=' \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "encrypted_serialized_key": "Aes256Gcm.MZQD65xuK3RTTBJ9GdcaV1Sv7yztXkl4JYXcn4yUAHexsnZAAJop5yIIvENicLuWQxng9ZjAwaaAlfEbIUAyyPfkmDkHUWlxItAUAWEdvr9EbJtUKrsCj4IP8lSKttV49LlowV-22j1zabiTxLWnGF_U1hrxqFSG2wtDX34upxvr2pZ-30z4LcYLUjFpW4Axbl5m2jbDM05jE64ND6uqG3xyUJNvXbgKpj_glSl4djJdya82ngRDTZpUE2j_QvQOakkahWXbC2jYnC6EcIS4tTgi5XUEjWY-R520rWtDUsJDCSwNbBtoi7asJOjodAHv5wCDK8ppGcEnK1ONE-e_YluBM9tcujGh9MF0cW2ntM_aW41w5rKNgRr7hsbaD-3-bf1QpZZYcxov5nlAjAAAHYsucEW2xFJUC0-yLjVZJx-mjtYzxKTbDeZF4ZXiyk-UzAt4I0zzS6lgmW2SWG6RhMLm1rMKI3hOjAJZbUk-wBMAKLidTCmz3S7YZWmqa9FylLLEGbbG3U5WGJ9ApJhDiqok0MEUAY2hA3oV56768MGHqM9qQGtJiBVJc9P2YroFn_ApfdkP75aZRWpka8xAmFn-BsZfhuPRoLTWtoy_Bm0OyOdUgYeXcGz3D3CmmB-M8s-jY3QoPQRjHtrfMIdoKMTOVxwm57OTjRSUFHmsFmxvOzK4dRh49uvrbieMhtkBZVOTb_N6ra4__V2JzTNfucPfNT2ncc9AyKRn7QxQpRR-4spyo-WzINtNAlT47ApffnSqOEQ6ICxvlmSDvE3c53ixqdw8dVoX9MqgCn8i3jZYsY2cfYYplCJSnHnN3hmSlbmAH3LFLSemsT0yZINOWFeni9cn1dTh3KSbmX0bqOKsKVoNPfycmgftk5CRbctMFHTferHwlrOd36emr9VvWO5-Qg88ToRbCBXoAAW7XAxHk0g91AGeBFxCMc490d1mC--fMiEixoMZvAUzhOMaVb9s-UI6LJGIVvmjxn1yako2fvg8gCoAdaWJ4UVeRyHtFAWd7MhBWINXQB2G78F7bcOgSlYtWt94L4LkalSaMWDxwyzlXg7dowvBiHslMKRuAye2cHojuK35IeZ5ml8NOsCLekD2hQzOw6OxH7Zd9MaZWnJblTk-Rm2Ezn-h8xBQJu609b7iCnn7w7JljiVrKKha6W3sOWXfyUoOFFo1EuyNT-noMEbO1hlLx2izmMcgaHfusu2ipxIu36WwFvVbp27Iic1j1XTLubWQGPOzkyoihbBOuKGax895NKkqtv590Oe-iqsFptyPgTSFHDpd5x4J_XOjb-c02E3m6i7kicJVtyiFSrLkqk1sOZPvOiABHsao2bmVN_sweDsOLo-v-cjOAS0do6nZprfS5EtNqMVoWfWVgT5uBlu-IJlPPuGz4PpQOYJK7FVW5cQ1ioCA16OMwBFGMy1Tn3M-E4NvSLF0NOJ5CjWnlZW2kkMB2KurUjVhAwI6EE0Cq1G_ZbIy2gv1eNjiqfUo3ShrlaEiSlJsc63PeEYwKrX2lXtZbQYoKiE2eOmjXnCSf7lw99dIoaO_fONqlPdkQYtwn-DE6n_6q6ItH3HadfJh7FlSNjOY1TDD1V3j5BJaG4sbKGPyBr0xjJzcEEoznkihBB0-FkRXX98kE63U8osF1K1SqWg9eG8Kja2XSkUGdJOZy4qEF8V6cIaJLBAmRzQSIPKF3ukgIHWd8ZPbOCkBctNPo6n1di7_ZsAejWO7zgRSgpZl0JR2Uj31HtqsxZt4vBYe3c-ac_Xa8mNr3wnImNPNhq28gpwYO2sB6hSvZ9sIfdmpOrbAqJozo45IyFeTNVDzeqs4HSgJ5VVkE7_vvF5tT-loxJ42aI84I1d76pzoP5VqC4OLlpPqRM-p_uMwHvhst-3fl6Zrwst-N8V2WSuka8paP_zfUHA_7R8jluder5N1pDUi-KXyXBYQR6BMtrxbiry64QCs258x3EBwJMZ7PSOxVcTsjrZCHAjYyS-e_NU5TNEWx7dp3EDPZPXLW9GUmzAZdmQtcxLjiXKiGoNDY_eAYAJOQ7vIOho-EljL1nUPyld6c64snTbTwx6dI2bySQ-6MXRUnPz1F3HRctxwyjsTXfHeI4rK1b9sVZln_Y6U0gU7YZ-ISsM4EINz1iH3aTYHpAF2IQvcO1tOtmPEhmfIm-F2LUATJi1BEhJWsFuZXCBDuCh9sBSjkWjbywj7jDT6WFjoqsnDRmTmjPKc39asPC-Oz4z4i4OhTY8yWjLIn_AB0MjgML0b3UJtQBqDZ40VOazV1IeirdHe2ZJQCnCsYL01bVCC-X0W0dxH7q9Ihx7iXOlLBl2KQyUXhjVukr2VAwGvvSXs8cIhV6V1V-BYm-EgWEgnZIS5agvBm65JqFr-jz7IoXftH8wiWC2F1SdDL5bO96FvsvcDNcR0v4kfg4J3XG6st0gXf6Qh3DkpPB90jWuK9VD_oEq861GiP8fjRWgzTin-e3Oz4jr1P4qoWyb1Wxbm6V8r_QgH-IBubiflrTSXV9D0lOanku8zOKcLj-bhMwg6k0yIknU4kiNAySCONIkEst32tgSMa4_Q2cb3v1ygVv5zwxETUPwINGtpj7ON9bRTeoGlAX2QkINfcJDYdm74RxuVBRKr4WR-d2VvaWAGLEZTJhLKNgTPbJevPay16rSuLJJzKKrKIdFLkJ4TzYkKQinHoe5sDk-zOcKMLR1qzk7S6a2HW8l4YDa_yJxgsNlbPs5RI0WBaFBAsMXFaBR5wcBgxj9RmZgBtsWqhrcpjz-gP4cylTThpE_YAfLEGEQklHmZ6LQ0jSf6D4_GlyDHM8k9EAqiFhrGvRnKfV6z3mWuxRoPoyaeFfIxHgvCcKf7_MqyTIVIlUuMPl92bO1vDpkNAnFbsCGe82dObHVhoH3gWXV_BeETT0ceocOvv0YEw_D8kGfscH3H7ZY4j-E4plWdNF-6WBYc7YKm6TAuqS4cATFLz2Syhm1aGbtQpW07hFScsSIBsrh9xBqgF7i66j47kO33Uta-dn9bgN9wZzm9-l7a5Lpg4N9nYVu0QRsg307HsLJIa5DyeraI_aIGRggrGGoqOFfx-Ufgb0RMB-v5uH5_ns0oIW0iOKN6Rf8PtUDlFCM4IvJ6pgOvbdqAikxEaarShr42I0WTulFKmO6ssZeqhrkAnNktLx1_aHSV7TLJrHZgVCTn7TnF_I_NEWDSejm0UqsZXjdDD-7lOndmxpB8dJq0Emp2Jw6GwESjL00kUcGJo_qLzjKSagxgJsnoTRlpjMrqFziI5YqZeE_MpznWqtnnjjAvpA2wFsmY_X1suOy_jINDFqDbDhtSAmf7eC1fw1Iif1cRVddzGvMQQ5-oy4bGZVTZgordPcYutOFqqrBEoD_8unDmV_c_Se4to-RFrJPKQIt1IPNabCG9En6fsm2VTeYn6tIQeXXOoN1HpJWqesIOGYQR3OJpH3vlnitVsAQEgTJWVYyEFdOXn2XYVuTx23OXkHH5a1-TKpynJDZwcEipGM7V1PcmM175cdMzLyTC5J6rukR_S6P-GkqY5NkA-_AprDwaWKTLLmVLlGzdldEvhDWBcUcC6Yb1r3aPP88DCPB7VjImQ41VlojaZxrTXKnQTPrJKGvxXQvob6OMCuGS5kALpDAIez9S-d2F47pLDi9zPZFjw8cC6S3jcZT5a4h8m2r26PqhjxhVLyx4PDXMkbrdASPw5ucOwyswipS-DMvkUAJltkd80JB2XjLwUXy-y5VAxplVyDJawjp0xlUYF8tLT20Bz2rsJEhz3ZOQCK3te99EdHAt5vKTacoviXDIMad9aPNuH9LMFInU4_YJxYkJfxanK_tOx3an7RGBBvQiWo2UUKkmouu6I1Zh8kcvOBPmy6tlNl8TWFccVThVytTEjOaopVw1-HGKA0XgvSNI22XFnXcIboPTy0dbmUZihiTxXOy2dQP3-tfvA3PPEp9A7ZLxwpuzj9JOC7eofGVC66puQm0nBSlQzS-bNUxmCSiAWJ8pIb9y8NeayBVcrxx9_kIbO5qsyHg3TROf6wVQqZDh7-TAwwhnoykc1c1aZ6BrVMhaPoUvtQNl_QLmCjfQ5AV9iEhcd-xhKB1ZM-oGrLhwOEoH82qOWMt-pkTbRHoFLjn2b3rF0V7dHpT8xbjTrQajUi08PEPWFGCudln5jueonn1E9maDFbt8LRbGT3xX7296dvjoAYRcvNQSlNaxCZrfuh1itdPqs-HMJM7NwpTZ1Unilep3weQBZMEoSu5iAoiZFUAKSSxp63UTh9qBoBRqCPHqRR_WqSMWVppOI5La0CFcXKV1.LS0tCml2OiAhYmluYXJ5IHwtCiAgN3o4SkthbGVkcjNGVm1mTwphdDogIWJpbmFyeSB8LQogIEVJaWRCUFdlejQxb0lnaUdFcXo0NkE9PQphZDogbm9uZQo=",
    "public_key": "-----BEGIN PUBLIC KEY-----\nMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA8ZEqPqHpVgWvK+NT0SR2\noVom2dv6QStVeYqgFnKm6TvZy66Gl8gQs6KjbgHc9dEfceN6yrE3WfvOwItgJSsY\nmsZVjuSyyXYtW3rVPhIEtsw/HaBcoC1HP3tuk7+YLq1UVjeOBtAt9yrhcGpkU2f4\nMi4Z7q1MVxErX2cdAbe+LLSx6Gx0n3hqPrmpMG6ZRoZ3dc4xNifs7K1RIt6hVSfy\nhIjgDr3Ret98CG+Vm5mJ3Vd5/BG0UE/j0NyKoGlqJg3VrJEVXcQoQ4DsUGj2GMu1\nj3DjyIn64ba4F2qpK4bJ6QcKGZsgr5K17KDguGBJ9B6HJ3PPd4oSSDHq7wfpu+bT\nNzN4NJ+eyIQJqYbwj1e18ETwHcogc2jpzGUHSEs1rTiUEibRFRUkqtlk4bXar/tz\n0II7bV9hIlhOaf9urqDwCtkN34p/h36eDqDxCaDeg4QtjBuZu9IgizrxHQZD5j8H\ntAhkULG6NqrAAynRxBDM+0kmZr+uzEL155YPnezIAoeCj8NlwkcUCm2ZCcQkA3AK\nvrkqGEOAm9QYqpyjpujjDDVgQNZXiQnWg94DG6CnRFqyFRPOfVKyhmcziqL1ejfV\ngPyrIHiU/5dmFu5LbmDjaUIX6j3DvYbXy87dPxsOESmeQWFQGY3REN+ZSwS+3FAP\n1aTS4Bwim9RiPycM3MY2vTUCAwEAAQ==\n-----END PUBLIC KEY-----\n",
    "metadata": {},
    "external_identifiers": [ "vault" ]
}'
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

## 11. Requesting Access To The Vault

It is time to set up access to the _**Vault**_.

In order to restrict users from creating more than one user account in the _**Vault**_, the vault account is created using an admission token we request from the _**Keystore**_. The _**Keystore**_ will request an admission token from the _**Vault**_ on behalf of the user:

There are two ways of doing this.

```bash
curl -X GET \
  https://meeco-api-dev.azure-api.net/keystore/external_admission_tokens \
  -H 'Content-Type: application/json' \
  -H 'Authorization: SESSION_AUTHENTICATION_STRING' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
```

Response:

```javascript
{
  "external_admission_token": {
    "vault_api_admission_token": "23fbb0ed4792787f799f",
    "passphrase_store_admission_token": null
  }
}
```

If you have access to the _Provider API Key_ you can add this in the headers of a `POST` to `https://meeco-api-dev.azure-api.net/vault/admission_tokens`

```bash
curl --request POST \
  --url https://meeco-api-dev.azure-api.net/keystore/admission_tokens \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -H 'authorization: PROVIDER_API_KEY' \
  -H 'content-type: application/json' \
```

Response:

```javascript
{
  "admission_token": {
    "id": "5d9e51dd-e6ad-49f8-ae4a-d2bcb0567146",
    "token_value": "TOKEN_VALUE_TO_ADD_TO_VAULT_USER_ACCOUNT_CREATION_CALL",
    "used": false
  }
}
```

## 12. Creating a Vault User Account

An _admission token_ can only be used once. To create a vault user account we'll submit:

* The _admission token_
* The _public key_ from the keypair we generated at step 09.

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/vault/me \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
    "user": {
      "public_key": "-----BEGIN PUBLIC KEY-----\nPUB_KEY_CHARACTERS\n-----END PUBLIC KEY-----\n",
      "admission_token": "VAULT_API_ADMISSION_TOKEN_FROM_STEP_11"
    }
  }
}'
```

The response is as follows:

```javascript
{
  "id": "00fa198d-f086-40f7-ba38-38ffed1f7b0f",
  "encrypted_session_authentication_string": "Rsa4096.ENCRYPTED_SESSION_AUTH_STRING"
}
```

The response contains

* ID of the user in the _**Vault**_
* an encrypted session token

Once decrypted, the encrypted session token can now be used with the `Authorization` header to work with the _**Vault**_.

## 13 Decrypting the encrypted session token

The encrypted session token is encrypted with the public key we used to creating a _**Vault**_ user account. We can decrypt it with script `13.decrypt_encrypted_access_token.rb` submitting the following arguments:

* The encrypted session token fom the previous step
* The unencrypted private key from step 09

We could have used the encrypted private key, but we have already demostrated the whole decryption chain, and here are are taking the shortcut to focus on the importnat part to avoid too much complexity.

```bash
./13.decrypt_encrypted_access_token.rb  Rsa4096.oIRrYsd2e_LRoSEd_G2XzQvmZ4-SEyPVH7sysrgb26XEB8kolNbhkq7XqpN20weHwrKF4av3f-izXdR5ep37aJhSBK0OXnLy84oOS5xQ6ngS_TuvOeBen_kNdAY2Hpy38hAgbIaTlAq_bzWhRqAQxz9kYC0S-duJbdAAkZ9x0X71tN0mMw6fs0Q4M86Hz_j9NP3RRC-cjL-kpIZoU-tJ1foxlZoXwpZVSJ_v5jHcKrxqu_tWr1tujDf1w_tbvvanEOZVthcKCbeMCLdkRYbIKDes-601W5J0wze7jZRDsgdpJfShVvdKW0Ja8MGeahusXYzxMYoqyUSYZIBwkx3juL8ULR8iip_a9euj8SU6vwSz7EZ_D_McmO4JSxjFldhv_27aEB9yjME9SkScbxOHZqdpZs7dtUJeu4QHRrOAAE5g1FG99i6coXecvhl_EJRzAcGK1OoMKn1l2xsOy87ish_4dfmVDzh0jYhe_g2BX9lcv9Y-oL9wHIXN7qtXea6osZOtTsV1xBDEqLmTKRQH--faacapsk4KzN8aXjsq9uAohUMOlibNbA_7zsKIZqn3spcWne8ht7nvThZYSdUHLcbjQ37GoV_b4ZQkocRwdhJYYqgT-hcAWCTQ42LcKAeIK8utEAm_ySImgMwyea-Szh1q9mIejRbbCjIsMPNOVWg=.LS0tIHt9Cg== "-----BEGIN RSA PRIVATE KEY-----\nPRIVATE_KEY\n-----END RSA PRIVATE KEY-----\n"
vhZtnNN3Xqh9MyWm-c4m <-- This is used as the "Authorization: TOKEN" to gain access to the vault. -->
```

The important code in the script is as follows:

```ruby
Cryppo::EncryptionStrategies::Rsa4096
  .new
  .decrypt(private_key, Cryppo.load(encrypted_token))
```

Now the encrypted session token can now be used with the `Authorization` header to work with the _**Vault**_.

## 14. Logging In Into The Vault

To login into the vault we post the same public key

```bash
curl -X POST \
  https://meeco-api-dev.azure-api.net/vault/session \
  -H 'Content-Type: application/json' \
  -H 'Meeco-Subscription-Key: DEVELOPER_PORTAL_SUBSCRIPTION KEY' \
  -d '{
  "public_key": "-----BEGIN PUBLIC KEY-----\nPUB_KEY_CHARACTERS\n-----END PUBLIC KEY-----\n"
}'
```

The response is as follows:

```javascript
{
  "session": {
    "encrypted_session_authentication_string": "Rsa4096.ENCYPTED_SESSION_AUTH_STRING"
  }
}
```

Once decrypted, encrypted session token can now be used with the `Authorization` header to work with the _**Vault**_.

