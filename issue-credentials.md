# Issue Credentials
Issue credential is the first operation in the lifecycle of a [Verifiable Credential](https://www.w3.org/TR/vc-data-model/#lifecycle-details). It assists the Issuer in creating the credential so it can be delivered to the subject of the credential.

The supported modes are:
* Generating credential – generates an (unsigned) credential that requires signing.
* Issuing credential – credential is generated and signed with keys managed on the platform.

The response is a JSON formatted verifiable credential with a system-generated unique id and issuance date.

## Prerequisites
* [Credential Schema](credential-schemas.md)
* [Credential Type](credential-types.md)
* [DID](dids/methods/did-web/md)
* Data as defined in the [Credential Schema](credential-schemas.md) and [Credential Type](credential-types.md)

## Who can use this?
An onboarded organisation in the tenacy set up as issuer by the tenant.

## Additional considerations
VC ids are stored in a Verifiable Data Registry which acts as an inventory of identifiers and schemas. The  registry 'StatusList2021' allows to write and read the status of the credential (ie. revoked, suspended). 

Other examples of verifiable data registries include trusted databases, decentralized databases, government ID databases, and distributed ledgers.

## Generate a Credential
Generating a credential requires issuance data (type, issue date, issuer DID) and data related to the subject (subject DID, claims) to be structured following the datamodel [W3C VC-Core](https://www.w3.org/TR/vc-data-model/#core-data-model) and respect the structure of the chosen credential schema. The endpoint resolves the DIDs and performs a number of coherency checks. The result is a JSON ready to be signed.

**Endpoint**

```bash
POST	/credentials/generate
```

**Request**

* Subject DID
* Claims about the Subject
* Expiration date

**Responses**

An unsigned credential in JSON format with 
* ID of the credential
* Credential Status
* [Credential Type](credential-types.md)
* issuanceDate

```bash
{
    "credential": {
        "@context": ["https://www.w3.org/2018/credentials/v1", "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/context"],
        "id": "uuid:of:vc",
        "type": "VerifiableCredential",
        "issuer": "did:example:CCnF3zFaXkPN4zB94XaomRdvw2zX3XHPVX3aExcgo6PV",
        "issuanceDate": "2010-01-01T19:23:24Z",
        "expirationDate": "2020-01-01T00:00:00Z",
        "credentialSubject": {
            "id": "did:of:subject",
            "type": "DriverLicenceCredential",
            "givenName": "Scrooge",
            "familyName": "McDuck",
            "endorsements": [{
                "licenceClass": "C"
            }, {
                "licenceClass": "R",
                "licenceType": "L"
            }],
            "address": {
                "streetAddress": "123 Road Street",
                "addressLocality": "Suburb",
                "addressRegion": "QLD",
                "postalCode": "1234"
            },
            "dateOfBirth": "1980-02-01",
            "licenceNumber": "123456",
            "conditions": ["corrective lenses"],
            "status": "ok"
        },
     
        "credentialSchema": {}
    },
    "credentialHash": "7L6ZqXZzusWvMfzRCTrzjan2AgPFotQzfWqdzXwVNHkV"
}
```

## Issue Credential
[Generate](#generate-credential) and sign a credential for the given issuer. The result is a signed vc-jwt.

To sign the credential, a fully qualified DID string or object with `id` property needs to be provided. The DID and its verification keypair needs to be under control of the platform to be able to perform the signing.

**Endpoint**

```bash
POST	/credentials/issue
```

**Request**

* Type – Encryption algorythm
* Private key - from the issuers's contolled assymetic keypair

**Responses**

The signed VC-jwt is created.

```bash
{
    "credential": {
        "@context": ["https://www.w3.org/2018/credentials/v1", "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/context"],
        "id": "uuid:of:vc",
        "type": "VerifiableCredential",
        "issuer": "did:example:CCnF3zFaXkPN4zB94XaomRdvw2zX3XHPVX3aExcgo6PV",
        "issuanceDate": "2010-01-01T19:23:24Z",
        "expirationDate": "2020-01-01T00:00:00Z",
        "credentialSubject": {
            "id": "did:of:subject",
            "type": "DriverLicenceCredential",
            "givenName": "Scrooge",
            "familyName": "McDuck",
            "endorsements": [{
                "licenceClass": "C"
            }, {
                "licenceClass": "R",
                "licenceType": "L"
            }],
            "address": {
                "streetAddress": "123 Road Street",
                "addressLocality": "Suburb",
                "addressRegion": "QLD",
                "postalCode": "1234"
            },
            "dateOfBirth": "1980-02-01",
            "licenceNumber": "123456",
            "conditions": ["corrective lenses"],
            "status": "ok"
        },
        "proof": {
            "type": "RsaSignature2018",
            "created": "2018-06-18T21:19:10Z",
            "proofPurpose": "assertionMethod",
            "verificationMethod": "https://example.com/jdoe/keys/1",
            "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMBs      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1THn5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeRogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
        },
        "credentialSchema": {}
    },
    "credentialHash": "7L6ZqXZzusWvMfzRCTrzjan2AgPFotQzfWqdzXwVNHkV"
}
```
