This page describes how to perform the following operations for `did:key` using the SVX platform.

- Resolve
- Create

## Resolve

```bash
curl -H "Authorization: Bearer TOKEN" \
     -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"' \
     -X GET "https://svx-api.meeco.me/did/{did:key identifier}"
```

## Create

### Generate Keypair

[Create your DID controller keypair](../did-controller-keypair.md)

Encode the public key using Base64 URL encoding.

```bash
cat pubkey | tail -c +13 | basenc --base64url
# e.g. YeAEwLNEJfHRVMSOs-Fr0C5mW9OFt3GACXtM5A7q7fo=
```

### Create DID

Call DID Create API to create the new DID and return the associated DID document. Use the Base64URL representation of the public key from previous step.

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://svx-api.meeco.me/did/create?method=key" \
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

# e.g.
# {
#   "jobId": null,
#   "didState": {
#     "did": "did:key:z6Mkm3KiSaczpWZuLHM19UR3f8cTRk6QPuzagCc2n2XvYRgV",
#     "state": "finished",
#     "secret": {
#       "verificationMethod": [
#         [
#           {
#             "id": "#temp",
#             "purpose": [
#               "authentication"
#             ]
#           },
#           {
#             "id": "did:key:z6Mkm3KiSaczpWZuLHM19UR3f8cTRk6QPuzagCc2n2XvYRgV#z6Mkm3KiSaczpWZuLHM19UR3f8cTRk6QPuzagCc2n2XvYRgV",
#             "controller": "did:key:z6Mkm3KiSaczpWZuLHM19UR3f8cTRk6QPuzagCc2n2XvYRgV",
#             "purpose": [
#               "authentication",
#               "assertionMethod",
#               "capabilityInvocation",
#               "capabilityDelegation",
#               "keyAgreement"
#             ]
#           }
#         ]
#       ]
#     }
#   },
#   "didRegistrationMetadata": {
#     "duration": 65,
#     "method": "key"
#   },
#   "didDocumentMetadata": null
# }
```