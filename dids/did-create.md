---
description: How to create a DID KEY, WEB & INDY using client secret mode
---

# Create DID KEY

## Meeco API

_Guide using the Meeco API_

### Generate Keypair for DID KEY

Create a new DID controller keypair (Ed25519) using openssl:

\*\*NOTE: make sure you have openssl & GNU coreutils installed and avalible on command line (e.g. brew install coreutils)

```bash
openssl genpkey -algorithm ed25519 -outform DER >privkey
openssl pkey -in privkey -pubout -out pubkey -inform DER -outform DER
```

Convert DID controller public key to Base64URL:

```bash
cat pubkey | tail -c +13 | basenc --base64url
```

### API request

Call DID Create API with following payload to create new DID KEY

_Request with above generated pubkey base64url string_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/create?method=key" \
     -H "Content-Type: application/json" \
     -d '{
           "options": {
             "clientSecretMode": true
           },
           "secret": { },
           "didDocument": {
             "@context": ["https//www.w3.org/ns/did/v1"],
             "verificationMethod": [{
               "id": "#temp",
               "type": "JsonWebKey2020",
               "publicKeyJwk": {
                 "kty": "OKP",
                 "crv": "Ed25519",
                 "x": "{Replace_With_Above_Generated_Base64URL_String}"
               }
             }]
           }
         }'
```

_Response with DID Identifier_

```bash
{
....
....
    "state": "finished",
    "did": "{New_DID_KEY_Identifier}",
....
....
}
```

Resolve DID

```bash
curl -X 'GET' \
  'https://identity-network-dev.meeco.me/did/{Replace_With_DID_KEY_Identifier}' \
  -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"'
```

## Meeco JS SDK

_Guide using the Meeco JS SDK_

### For SDK installation & basic usage, visit SDK-Demo sample application [SDK-JS-DEMO](https://github.com/Meeco/js-sdk/blob/develop/packages/sdk-demo/src/did-management/index.ts)

Create a new DID controller keypair (Ed25519)

```js
const array = new Uint8Array(32);
const secret = self.crypto.getRandomValues(array);
const keyPair = new Ed25519(secret);
console.log(`public key hex: ${keyPair.getPublicKeyHex()}`);
console.log(`private key hex: ${keyPair.keyPair.getSecret('hex')}`);
```

Create new DID KEY

```js
let did: DIDBase = new DIDKey(keyPair);

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
const generatedDID = await api.create({}, did);
console.log(generatedDID);
```

Resolve DID

```js
const result = await api.resolve({}, identifier);
console.log(result);
```

# Create DID WEB

## Meeco API

_Guide using the Meeco API_

### Generate Keypair for DID WEB

Create a new DID controller keypair (Ed25519) using openssl:
\*\*NOTE: make sure you have openssl & GNU coreutils installed and avalible on command line (e.g. brew install coreutils)

```bash
openssl genpkey -algorithm ed25519 -outform DER >privkey
openssl pkey -in privkey -pubout -out pubkey -inform DER -outform DER
```

Convert DID controller public key to base58:

```bash
cat pubkey| tail -c +13| base58
```

### API request

Call DID Create API with following payload to create new DID WEB

_Request with above generated pubkey base58 string_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/create?method=web" \
     -H "Content-Type: application/json" \
     -d '{
            "options": {
                "clientSecretMode": false
            },
            "didDocument": {
                "verificationMethod": [
                {
                    "id": "did:web:did-web-meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1",
                    "type": "Ed25519VerificationKey2018",
                    "publicKeyBase58": "{Replace_With_Above_Generated_Base58_String}"
                }
                ],
                "service": [
                {
                    "type": "LinkedDomains",
                    "serviceEndpoint": "meeco.me"
                }
                ],
                "authentication": [
                "did:web:did-web-meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1"
                ],
                "assertionMethod": [
                "did:web:did-web-meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1"
                ]
            }
        }'

```

_Response with DID Identifier_

```bash
{
....
....
{
  "didState": {
    "state": "finished",
    "did": "{DID_WEB_Identifier}"
  },
....
....
}
```

Resolve DID

```bash
curl -X 'GET' \
  'https://identity-network-dev.meeco.me/did/{Replace_With_DID_WEB_Identifier}' \
  -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"'
```

## Meeco JS SDK

_Guide using the Meeco JS SDK_

### For SDK installation & basic usage, visit SDK-Demo sample application [SDK-JS-DEMO](https://github.com/Meeco/js-sdk/blob/develop/packages/sdk-demo/src/did-management/index.ts)

Create a new DID controller keypair (Ed25519)

```js
const array = new Uint8Array(32);
const secret = self.crypto.getRandomValues(array);
const keyPair = new Ed25519(secret);
console.log(`public key hex: ${keyPair.getPublicKeyHex()}`);
console.log(`private key hex: ${keyPair.keyPair.getSecret('hex')}`);
```

Create new DID KEY

```js
const did: DIDWeb = new DIDWeb(keyPair);
const verificationMethodId = did.setVerificationMethod();
did
  .setAssertionMethod(verificationMethodId)
  .setAuthentication(verificationMethodId);

did.didDocument.service = [
  {
    type: 'LinkedDomains',
    serviceEndpoint: 'meeco.me',
  },
];

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
const generatedDID = await api.create({}, did);
console.log(generatedDID);
```

Resolve DID

```js
const result = await api.resolve({}, identifier);
console.log(result);
```

# Create DID Indy

## Meeco API

_Guide using the Meeco API_

### Generate Keypair for DID Indy

Create a new DID controller keypair (Ed25519) using openssl:

```bash
openssl genpkey -algorithm ed25519 -outform DER >privkey
openssl pkey -in privkey -pubout -out pubkey -inform DER -outform DER
```

Convert DID controller public key to base58:

```bash
cat pubkey| tail -c +13| base58
```

### API request

Call DID Create API with following payload to create new DID Indy

### Step 1

_Request with above generated pubkey base58 string & ledger network danube_

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/create?method=indy" \
     -H "Content-Type: application/json" \
     -d '{
           "options": {
             "clientSecretMode": true,
             "network": "danube"
           },
           "secret": { },
           "didDocument": {
             "@context": ["https//www.w3.org/ns/did/v1"],
             "verificationMethod": [{
               "id": "#key-1",
               "type": "Ed25519VerificationKey2018",
               "publicKeyBase58": "{Replace_With_Above_Generated_Base58_String}"
             }],
             "service": [{
               "id": "#testServiceId",
               "type": "LinkedDomains",
               "serviceEndpoint": "https://meeco.me/"
             }]
           }
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

### Step 3

_decode base64 serializedPayload payload from above resonse_

```bash
echo "<-- base64 encoded -->"|base64 -d >payload
```

_sign payload with DID controller private key_

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
               "signingRequestAttrib": {
                 "signature": "<-- base64 encoded -->"
               }
             }
           },
           "didDocument": { }
         }'

```

_final response_

```bash
....
  "didState": {
    "state": "finished",
    "did": "{DID_WEB_Identifier}",
....
```

Resolve DID

```bash
curl -X 'GET' \
  'https://identity-network-dev.meeco.me/did/{Replace_With_DID_WEB_Identifier}' \
  -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"'
```

## Meeco JS SDK

_Guide using the Meeco JS SDK_

### For SDK installation & basic usage, visit SDK-Demo sample application [SDK-JS-DEMO](https://github.com/Meeco/js-sdk/blob/develop/packages/sdk-demo/src/did-management/index.ts)

Create a new DID controller keypair (Ed25519)

```js
const array = new Uint8Array(32);
const secret = self.crypto.getRandomValues(array);
const keyPair = new Ed25519(secret);
console.log(`public key hex: ${keyPair.getPublicKeyHex()}`);
console.log(`private key hex: ${keyPair.keyPair.getSecret('hex')}`);
```

Create new DID KEY

```js
const did = new DIDIndy(keyPair);
did.didDocument.service = [
  {
    type: 'LinkedDomains',
    serviceEndpoint: 'meeco.me',
  },
];

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
const generatedDID = await api.create({}, did);
console.log(generatedDID);
```

Resolve DID

```js
const result = await api.resolve({}, identifier);
console.log(result);
```
