# Issue Credentials

Issue credential is the first operation in the lifecycle of a [Verifiable Credential](https://www.w3.org/TR/vc-data-model/#lifecycle-details). It assists the Issuer in creating the credential so it can be delivered to the subject of the credential.

The supported modes are:
* Generating credential – generates an (unsigned) credential that requires signing.
* Issuing credential – credential is generated and signed with keys managed on the platform.

The response is a JSON formatted verifiable credential with a system-generated unique id and issuance date.

## Prerequisites

* [Credential Schema](../credential-schemas.md) – [data schema](https://www.w3.org/TR/vc-data-model/#data-schemas) of the credential
* [Credential Type](../credential-types.md)
* [DID](../dids/did-methods.md)

## Who can use this?

An onboarded organisation in the tenacy set up as issuer by the tenant.

## Generate a Credential

Generating a credential requires issuance data and data related to the subject. This data is structured following the datamodel of [W3C Verifiable Credential Core Data Model](https://www.w3.org/TR/vc-data-model/#core-data-model) and following the structure of the chosen credential schema. The endpoint resolves the DIDs and performs a number of coherency checks. The result is a JWT ready to be signed.

**Endpoint**

```bash
POST /credentials/generate
```

**Request**

* [Credential Type](credential-types.md)
* Issuer
  * DID – fully qualified DID string
  * Name – Name of the issuer (optional)
* Claims – maps to the `credentialSubject` attribute of a credential
  * Subject DID – typically, the `id` property contains the DID of the subject
* Expiration date – datetime after which the credential expires
* Revocable – if true, the generated credential can be revoked later on.

**Responses**

The credential object that is generated. This contains

* ID of the credential
* Unsigned credential in `vc-jwt` data format
* Meta data

## Issue a Credential

[Generate](#generate-credential) and sign a credential for the given issuer. The result is a signed vc-jwt.

To sign the credential, a fully qualified DID string or object with `id` property needs to be provided. The DID and its verification keypair needs to be under control of the platform to be able to perform the signing.

**Endpoint**

```bash
POST /credentials/issue
```

**Request**

* [Credential Type](credential-types.md)
* Issuer
  * DID – fully qualified DID string
  * Name – Name of the issuer (optional)
* Claims
  * Subject DID – typically, the `id` property contains the DID of the subject
* Expiration date – datetime after which the credential expires
* Revocable – if true, the generated credential can be revoked later on.

**Responses**

The credential object that is generated. This contains

* ID of the credential
* Signed Credential in `vc-jwt` data format
* Meta data
