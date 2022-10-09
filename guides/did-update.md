---
description: How to update a DID WEB & INDY using client secret mode
---

# Update DID WEB

## Meeco API

_Guide using the Meeco API_

### API request

Call DID Update API with following payload to update existing DID WEB

_Request with did document_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/update?method=web" \
     -H "Content-Type: application/json" \
     -d '{
  "did": "{Replace_With_DID_WEB_Identifier}",
  "didDocumentOperation": [
    "setDidDocument"
  ],
  "options": {
    "clientSecretMode": false
  },
  "didDocument": {
    "id": "{Replace_With_DID_WEB_Identifier}",
    "verificationMethod": [
      {
        "id": "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1",
        "type": "Ed25519VerificationKey2018",
        "publicKeyBase58": "Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE"
      }
    ],
    "service": [
      {
        "type": "LinkedDomains",
        "serviceEndpoint": "updated.example.com"
      }
    ],
    "authentication": [
      "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
    ],
    "assertionMethod": [
      "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
    ]
  }
}'

```

_Successfully Updated response_

```bash
{
  "jobId": null,
  "didState": {
    "state": "finished",
    "didDocument": {
      "id": "{Replace_With_DID_WEB_Identifier}",
      "verificationMethod": [
        {
          "id": "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1",
          "type": "Ed25519VerificationKey2018",
          "publicKeyBase58": "Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE"
        }
      ],
      "service": [
        {
          "type": "LinkedDomains",
          "serviceEndpoint": "updated.example.com"
        }
      ],
      "authentication": [
        "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
      ],
      "assertionMethod": [
        "did:web:did-web-meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
      ]
    }
  },
  "didRegistrationMetadata": {
    "duration": 29,
    "method": "web"
  },
  "didDocumentMetadata": {

  }
}
```

Try Resolving DID - Should return updated DID WEB

```bash
curl -X 'GET' \
  'https://identity-network-dev.meeco.me/did/{Replace_With_DID_WEB_Identifier}' \
  -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"'
```

## Meeco JS SDK

_Guide using the Meeco JS SDK_

### For SDK installation & basic usage, visit SDK-Demo sample application [SDK-JS-DEMO](https://github.com/Meeco/js-sdk/blob/develop/packages/sdk-demo/src/did-management/index.ts)

Load keypair (Ed25519) from private key hex string

```js
const secretKeyHex = '{your_private_key_hex_string}';
const secret = Uint8Array.from(Buffer.from(secretKeyHex, 'hex'));
const keyPair = new Ed25519(secret);
```

Update existing DID Web

```js
const newDidDocument = '{put_updated_did_document}';
const did: DIDWeb = new DIDWeb(keyPair);
did.didDocument = newDidDocument;

const environment = new Environment({
  vault: {
    url: '',
    subscription_key: '',
  },
  keystore: {
    url: '',
    subscription_key: '',
    provider_api_key: '',
  },
  identityNetwork: {
    url: '{identity-network-url}',
    subscription_key: '{subsription-key}',
  },
});
const api = new DIDManagementService(environment);
const UpdatedDIDResult = await api.update(
  { identity_network_access_token: '{access-token}' },
  did
);
```

Try Resolving Updated DID

```js
const result = await api.resolve(
  { identity_network_access_token: '{access-token}' },
  identifier
);
console.log(result);
```

# Update DID Indy

## Meeco API

_Guide using the Meeco API_

### API request

Call DID Update API with following payload to update existing DID Indy

### Step 1

_Request with did document_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/update?method=indy" \
     -H "Content-Type: application/json" \
     -d '{
  "did": "{Replace_With_DID_WEB_Identifier}",
  "didDocumentOperation": [
    "setDidDocument"
  ],
  "options": {
    "clientSecretMode": true,
    "network": "danube"
  },
  "didDocument": {
    "id": "{Replace_With_DID_WEB_Identifier}",
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "verificationMethod": [
      {
        "type": "Ed25519VerificationKey2018",
        "id": "{Replace_With_DID_WEB_Identifier}#verkey",
        "controller": "{Replace_With_DID_WEB_Identifier}",
        "publicKeyBase58": "6sPub8K5pCPoz6cEDf17qbn3DKMxEtEPaAaof7bgczZy"
      }
    ],
    "service": [
      {
        "serviceEndpoint": "updated.example.meeco.me",
        "type": "LinkedDomains"
      }
    ],
    "authentication": [
      "{Replace_With_DID_WEB_Identifier}#verkey"
    ]
  }
}'

```

_Response with action signPayload - signing Request Attribute_

```bash
{
    "jobId": "{JOB_ID}",
    "didState": {
        "state": "action",
        "action": "signPayload",
        "signingRequest": {
            "signingRequestAttrib": {
                "kid": "#key-1",
                "alg": "EdDSA",
                "purpose": "authentication",
                "payload": { ... },
                "serializedPayload": "<-- base 64 encoded -->"
            }
        }
    },
    "didRegistrationMetadata": { ... },
    "didDocumentMetadata": { ... }
}

```

### Step 2

_decode base64 serializedPayload payload from above resonse_

```bash
echo "<-- base64 encoded -->"|base64 -d >payload
```

_sign payload with DID controller private key_

```bash
openssl pkeyutl -sign -rawin -in payload -inkey privkey -keyform DER| base64| tr -d '\n' >signature
```

_request with signed payload_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/update?method=indy" \
     -H "Content-Type: application/json" \
     -d '{
           "jobId": "{JOB_ID_From_Previous_Response}",
           "options": {
             "clientSecretMode": true
            },
           "secret": {
             "signingResponse": {
               "signingRequestNym": {
                 "signature": "<-- base64 encoded signature-->"
               }
             }
           },
           "didDocument": { }
         }'

```

_Successful updated Response_

```bash
{
  "jobId": "{JOB_ID}",
  "didState": {
    "state": "finished",
    "did": "{Replace_With_DID_Indy_Identifier}",
    "secret": { }
  },
  "didRegistrationMetadata": { ... },
  "didDocumentMetadata": { ... }
}

```

Try Resolving DID - Should return 404

```bash
curl -X 'GET' \
  'https://identity-network-dev.meeco.me/did/{Replace_With_DID_Indy_Identifier}' \
  -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"'
```

## Meeco JS SDK

_Guide using the Meeco JS SDK_

### For SDK installation & basic usage, visit SDK-Demo sample application [SDK-JS-DEMO](https://github.com/Meeco/js-sdk/blob/develop/packages/sdk-demo/src/did-management/index.ts)

Load keypair (Ed25519) from private key hex string

```js
const secretKeyHex = '{your_private_key_hex_string}';
const secret = Uint8Array.from(Buffer.from(secretKeyHex, 'hex'));
const keyPair = new Ed25519(secret);
```

Update existing DID Indy

```js
const newDidDocument = '{put_updated_did_document}';
const did: DIDIndy = new DIDIndy(keyPair);
did.didDocument = newDidDocument;

const environment = new Environment({
  vault: {
    url: '',
    subscription_key: '',
  },
  keystore: {
    url: '',
    subscription_key: '',
    provider_api_key: '',
  },
  identityNetwork: {
    url: '{identity-network-url}',
    subscription_key: '{subsription-key}',
  },
});
const api = new DIDManagementService(environment);
const UpdatedDIDResult = await api.update(
  { identity_network_access_token: '{access-token}' },
  did
);
```

Try Resolving Updated DID

```js
const result = await api.resolve(
  { identity_network_access_token: '{access-token}' },
  identifier
);
console.log(result);
```
