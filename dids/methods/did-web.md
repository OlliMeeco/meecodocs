This page describes how to perform the following operations for `did:web` using the SVX platform.

- Resolve
- Create

### Resolve

```bash
curl -H "Authorization: Bearer TOKEN" \
     -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"' \
     -X GET "https://svx-api.meeco.me/did/{did:web identifier}"
```

### Create

#### Generate Keypair

[Create your DID controller keypair](../did-controller-keypair.md)

Encode the public key using Base64 URL encoding.

```bash
cat pubkey| tail -c +13| base58
```

#### Create DID

Call DID Create API to create the new DID and return the associated DID document. Use the Base58 representation of the public key from previous step.


```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://svx-api.meeco.me/did/create?method=web" \
     -H "Content-Type: application/json" \
     -d '{
            "options": {
                "clientSecretMode": false
            },
            "didDocument": {
                "verificationMethod": [
                  {
                      "id": "did:web:did-web.meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1",
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
                  "did:web:did-web.meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1"
                ],
                "assertionMethod": [
                  "did:web:did-web.meeco.me:{Replace_With_Above_Generated_Base58_String}#key-1"
                ]
            }
        }'
```
