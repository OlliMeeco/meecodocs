# Issue Credentials
Issue credential is the first operation in the lifecycle of a [Verifiable Credential](https://www.w3.org/TR/vc-data-model/#lifecycle-details). It assists the Issuer in creating the credential so it can be delivered to the subject of the credential.
The supported modes are:
* Generating credential – generates an (unsigned) credential that requires signing.
* Issuing credential – credential is generated and signed with keys managed on the platform.

The response is a JSON formatted verifiable credential with a system-generated unique id and issuance date.

## Prerequisite
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
Generating a credential requires metadata about the credential (type, issue date, issuer) and its subject (subject DID, claims) to be structured following the datamodel [W3C VC-Core](https://www.w3.org/TR/vc-data-model/#core-data-model) and respect the structure of the chosen credential schema. The endpoint will fit the data into the schema and performs a number of coherency checks. The result is a JSON ready to be signed.

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
* id (of the credential)
* credentialStatus
* type 
* issuanceDate

```bash
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ], 
  "id": "http://meeco.me/credentials/1332",
  "type": ["VerifiableCredential", "MeecoCredential"],
  "issuer": "https://meeco.me/issuers/533041",
  "issuanceDate": "2022-04-01T03:23:24",
  "credentialSubject": {
    "id": "did:example:1c276e12ec21ebfeb1f712ebc6f",
    "UserOf": {
      "id": "did:example:ec21ebfebc276e121f712ebc6f1",
      "name": [{
        "value": "SVX"
      }]
    }
  }
}
```

## Sign a Credential
By signing a credential an entity adds proof to a generated credential, and thus the entity becomes the 'issuer' of the crendential. This allows another entity (the 'verifier') to trust the provenance of the crendential. The proof is established by signing a hash of the VC with the issuer's private key.

**Endpoint**

```bash
POST	/credentials/issue
```

**Request**

* type – Encryption algorythm
* private key - from the issuers's contolled assymetic keypair

**Responses**
A signed credential JSON format including:
* created – timestamp of the signature
* verificationMethod - location of the public key
* signature data - the value that can be verified using the public key

```bash
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ], 
  "id": "http://meeco.me/credentials/1332",
  "type": ["VerifiableCredential", "MeecoCredential"],
  "issuer": "https://meeco.me/issuers/533041",
  "issuanceDate": "2022-04-01T03:23:24Z",
  "credentialSubject": {
    "id": "did:example:1c276e12ec21ebfeb1f712ebc6f",
    "UserOf": {
      "id": "did:example:ec21ebfebc276e121f712ebc6f1",
      "name": [{
        "value": "SVX"
      }]
    }
  },
  "proof": {
    "type": "Ed25519Signature2022",
    "created": "2022-04-03T03:23:24Z",
    "verificationMethod": "https://meeco.me/issuers/21#key1",
    "proofPurpose": "assertionMethod",
    "proofValue":"n1PzZs6ZjWp1CktyGesjuTSwRdoWhAz58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCFmHKtz"
  }
}
```

## Revoke a Credential
When an issuer no longer wants to underwrite the validity of a crendential, the issuer can revoke or suspend it by updating its status. The statusses of  credentials are maintained in a publically available registry that allows entities to consult it when verifying a credential.

**Endpoint**

```bash
POST	/credentials/status
```

**Request**

* id - the id of the VC
* statusListIndex - The id of the associated status in the statuslist

**Responses**

The status list is updated with the new status associated to the crendential id.
