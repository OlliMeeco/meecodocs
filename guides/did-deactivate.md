---
description: How to deactivate a DID WEB & INDY using client secret mode
---

# Deactivate DID WEB

## Meeco API

_Guide using the Meeco API_

### API request

Call DID Deactivate API with following payload to deactivate existing DID WEB

_Request with did identifier_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/deactivate?method=web" \
     -H "Content-Type: application/json" \
     -d '{
            "did": "{Replace_With_DID_KEY_Identifier}",
            "options": {
              "clientSecretMode": false
            }
        }'

```

_Successfully deactivated response_

```bash
{
  "jobId": null,
  "didState": {
    "state": "finished"
  },
  "didRegistrationMetadata": {
    "duration": 13,
    "method": "web"
  },
  "didDocumentMetadata": null
}
```

Try Resolving DID - Should return 404

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

Deactivate existing DID Web

```js
const identifier = '{existing_did_web_identifier}';
const did: DIDWeb = new DIDWeb(keyPair);
did.didDocument.id = identifier;

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
const deactivatedDIDResult = await api.deactivate({}, did);
```

Try Resolving Deactivated DID

```js
const result = await api.resolve({}, identifier);
console.log(result);
```

# Deactivate DID Indy

## Meeco API

_Guide using the Meeco API_

### API request

Call DID deactivate API with following payload to deactivate DID Indy

### Step 1

_Request with above generated pubkey base58 string & ledger network danube_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/deactivate?method=indy" \
     -H "Content-Type: application/json" \
     -d '{
           "did": "{Replace_With_DID_Indy_Identifier}",
           "options": {
             "clientSecretMode": true
           },
           "secret": { }
         }'

```

_Response with action signPayload - signing Request Nym [Transaction on Ledger]_

```bash
{
    "jobId": "{JOB_ID}",
    "didState": {
        "state": "action",
        "action": "signPayload",
        "signingRequest": {
            "signingRequestNym": {
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

_sign payload with Transaction Endorser private key_

```bash
openssl pkeyutl -sign -rawin -in payload -inkey privkey -keyform DER| base64| tr -d '\n' >signature
```

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/create?method=indy" \
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

_Successful deativation Response_

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

Deactivate existing DID Web

```js
const identifier = '{existing_did_web_identifier}';
const did: DIDWeb = new DIDIndy(keyPair);
did.didDocument.id = identifier;

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
const deactivatedDIDResult = await api.deactivate({}, did);
```

Try Resolving Deactivated DID

```js
const result = await api.resolve({}, identifier);
console.log(result);
```
