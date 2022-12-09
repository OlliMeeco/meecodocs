# Issue a Credential
SVX allows you to generate, issue and revoke credentials following the [W3C data model](
https://www.w3.org/TR/vc-data-model/)

To issue a credential, an entity needs to control a did and keypair.  creates a credential and and signs it with its private key. The result is a JSON formatted verifiable credential with a unique id and a credentialStatus.

## Prerequisite
* Credential Schema 
* Credential Type
* Holder Wallet DID
* Data required as defined in credential schema/type. Typically:
  * Claims about the subject
  * Subject DID
  * Issue / Expiration data

## Who can use this?
An onboarded organisation in the tenacy with apropriate issuing rights set up by the tenant.

## Additional considerations
VC ids are stored in a Verifiable Data Registry which acts as an inventory of identifiers and schemas. The  registry 'StatusList2021' allows to store the status of the credential (active, revoked, suspended). 

Other examples of verifiable data registries include trusted databases, decentralized databases, government ID databases, and distributed ledgers.

## Generate a Credential
Generating a credential requires metadata about the credential (type,issue date, issuer, subject) and its subject (subject DID, claims) to be structured following the [W3C VC-Core standard](
https://www.w3.org/TR/vc-data-model/#core-data-model)
 and respect the structure of the chosen credential schema. The endpoint will fit the data into the schema and performs a number of coherency checks. The result is a JSON ready to be signed.

**Endpoint**

```bash
POST	/credentials/generate
```

**Request**

* Issuer DID
* Subject DID
* Claims about the Subject

**Responses**

An unsigned credential in JSON format with 
* id (of the credential)
* credentialStatus (initiatlly set to active)
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

## Issue a Credential
By Issuing a credential an entity adds proof to a generated credential, and thus the entity becomes the 'issuer' of the crendential. This allows another entity (the 'verifier') to trust it (or not) as it can check the provenance of the crendential. The proof is established by signing the hash of the VC with the issuer's private key.

**Endpoint**

```bash
POST	/credentials/sign
```

**Request**

* type – Encryption algorythm
* private key - from the contolled assymetic keypair

**Responses**
A signed credential JSON format with:
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
