# Quickstart

This guide aims to get you up and running and familiar with the Exchange API in about 15 minutes. We'll teach you how to

- create and manage DID
- issue and revoke Verifiable Credentials

**_NOTE:_** It assumes you already have an approved account in dev.meeco.me portal and subscription key to access exchange API.

you can signup for a developer account here https://dev.meeco.me/signup

---

## Create a DID for a holder

```curl

curl -d -i -v -X POST "https://sandbox.meeco.me/exchange/dids/hedera" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"

```

Response header

```http
...
exchange-private-key: 302e020100300506032b657004220420f4011a192ba599b54adca7f95dfbd73c22dca
000000000000000000000000000
...
```

Response body

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464",
  "publicKey": [
    {
      "id": "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464#did-root-key",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464",
      "publicKeyBase58": "EKQfP6GMyhWuzZYw4QNTnrM4EgRPgccQViSAMPEsX7wz"
    }
  ],
  "authentication": [
    "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464#did-root-key"
  ],
  "assertionMethod": [
    "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464#did-root-key"
  ]
}
```

The above request generates a key and DID document for a new DID. Also registers the DID on Hedera when using the "hedera" method. The new private key used to create DID is returned in the header.

Note down DID `did:hedera:testnet:example;hedera:testnet:fid=0.0.78464`

---

## Issue Learning Credentials for Holder DID

Retrieve a list of credential types that can be issued by the exchange.

```curl

curl -i -v -X GET "https://sandbox.meeco.me/exchange/credentials/types" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"
```

Response

```json
{
  "credentials": [
    {
      "label": "QLD Driver Licence",
      "type": "DriverLicenceCredential",
      "available": true,
      "domainHint": "https://broker.sandbox.connectid.com.au/auth/realms/connectid",
      "schema": "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/schema.json",
      "contexts": [
        "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/context.json"
      ]
    },
    {
      "label": "NSW Driver Licence",
      "type": "DriverLicenceCredential",
      "available": false,
      "domainHint": "https://broker.sandbox.connectid.com.au/auth/realms/connectid",
      "schema": "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/schema.json",
      "contexts": [
        "https://vc-schemas.meeco.me/credentials/driverLicence/1.0/context.json"
      ]
    },
    {
      "label": "Learning Record",
      "type": "LearningRecordCredential",
      "available": true,
      "domainHint": "",
      "schema": "https://vc-schemas.meeco.me/credentials/learningRecord/1.0/schema.json",
      "contexts": [
        "https://vc-schemas.meeco.me/credentials/learningRecord/1.0/context.json"
      ]
    }
  ]
}
```

Issue a credential of the type `LearningRecordCredential`

replace `subjectDid` in following request with earlier created holder DID

```curl

curl -i -v -X POST "https://sandbox.meeco.me/exchange/credentials/types/LearningRecordCredential" \
-H "Content-Type: application/json" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key" \
-d '{"subjectDid": "did:hedera:testnet:5ZpDhGKtHb1LAnQE1ry2bw1YTkm3KWbru7TsYkfYEdq9;hedera:testnet:fid=0.0.78464","claims": {"status": "compliant","grade": "95","completionDate": "2021-01-02T00:00:00+00:00","course": {"id": "122345","courseCode": "ws101","name": "WorkSafe Basic Training Module","version": "2021-01-01T00:00:00+00:00","provider": "WorkPro Training"}},"expiration": "2025-01-01T00:00:00Z"}'

```

The new credential signed by the issuer's key will be returned

Response

```json
{
  "credential": {
    "@context": [
      "https://vc-schemas.meeco.me/credentials/learningRecord/1.0/context.json",
      "https://www.w3.org/2018/credentials/v1"
    ],
    "type": ["VerifiableCredential"],
    "issuer": {
      "id": "did:hedera:testnet:3UehyMhjQNDVWSPHEcpBRfPB5gYLtRxukHfJgV2sdfew;hedera:testnet:fid=0.0.78464"
    },
    "credentialSubject": {
      "completionDate": "2021-01-02T00:00:00+00:00",
      "course": {
        "courseCode": "ws101",
        "id": "122345",
        "name": "WorkSafe Basic Training Module",
        "provider": "WorkPro Training",
        "version": "2021-01-01T00:00:00+00:00"
      },
      "grade": "95",
      "status": "compliant",
      "type": "LearningRecordCredential",
      "id": "did:hedera:testnet:example;hedera:testnet:fid=0.0.78464"
    },
    "proof": {
      "type": "Ed25519Signature2018",
      "proofPurpose": "assertionMethod",
      "created": "2021-06-10T03:29:57.000Z",
      "verificationMethod": "did:hedera:testnet:3UehyMhjQNDVWSPHEcpBRfPB5gYLtRxukHfJgV2sdfew;hedera:testnet:fid=0.0.78464#did-root-key",
      "jws": "Iy0SNzS6tRWz__nIt8Fb4qwkEntuY0sZm6j8osEHsAhCJGog84aVRVqOaflcwW3L1NJnofqex-wQbFugWonEBA"
    },
    "issuanceDate": "2021-06-10T03:29:57.000Z",
    "id": "urn:uuid:c8402ffe-14b4-4ff9-857c-1763459f17d2",
    "expirationDate": "2025-01-01T00:00:00.000Z",
    "credentialSchema": {
      "id": "https://vc-schemas.meeco.me/credentials/learningRecord/1.0/schema.json",
      "type": "JSONSchemaValidator2019"
    }
  },
  "credentialHash": "2fPrkyXhHmEXDFVkAPLj621e5sLBktPvZPDMBnroTRsZ"
}
```

note down id of credential `urn:uuid:c8402ffe-14b4-4ff9-857c-1763459f17d2`

---

## Retrieve credentials issued for a DID

An Issuer can issue credentials to be held by a particular DID, These are stored encrypted by the subject's public key, and can be downloaded by the wallet.

```curl
curl -i -v -X GET "https://sandbox.meeco.me/exchange/credentials/stored/did%3Ahedera%3Atestnet%3AES6uGn1298qRYpM9Y6n5aj8DtPnpZHZXasdfedfeere%3Bhedera%3Atestnet%3Afid%3D0.0.78464" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"
```

returns encrypted JWT Verifiable credentials that can be decrypted by the holder's private key and store in a wallet.

Response

```json
[
  {
    "vc": "mkCF7XbWuojmO4wXkt1IliGUQ4K3FIZHK9eOfzTdfIG9csfk065Gt1ZJbITQSJRjN0UJ+thI4aKHErA5tWsVhKR8Lf9bQhwe48FgnMMjy8kv/...",
    "nonce": "BxM5p+PfTlsDKx2+2jCrSlf73bOB9Vg4"
  }
]
```

---

## Retrieves the status of issued credentials

use previously noded cred id to retrieve the current status of issued credentials

```curl
curl -i -v -X GET "https://sandbox.meeco.me/exchange/credentials/urn%3Auuid%3Ac8402ffe-14b4-4ff9-857c-1763459f17d2" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"
```

Response

```json
{
  "status": "ACTIVE"
}
```

---

## Revoke issued Credentials

Issuers can only revoke credentials they have issued

```crul
curl -i -v -X DELETE "https://sandbox.meeco.me/exchange/credentials/urn%3Auuid%3Ac8402ffe-14b4-4ff9-857c-1763459f17d2" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"
```

Response

```http
HTTP/1.1 202 Accepted

Accepted
```

---

## Verify credentials status after revoking

use previously noded cred id to retrieve the current status of issued credentials

```curl
curl -i -v -X GET "https://sandbox.meeco.me/exchange/credentials/urn%3Auuid%3Ac8402ffe-14b4-4ff9-857c-1763459f17d2" \
-H "Meeco-Subscription-Key: replace-it-with-your-subscription-key"
```

Response

```json
{
  "status": "REVOKED"
}
```
