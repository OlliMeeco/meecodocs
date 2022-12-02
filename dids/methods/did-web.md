This page describes how to perform the following operations for `did:web` using the SVX platform.

- Resolve
- Create
- Update
- Deactivate

## Resolve

```bash
curl -H "Authorization: Bearer TOKEN" \
     -H 'accept: application/ld+json;profile="https://w3id.org/did-resolution"' \
     -X GET "https://svx-api.meeco.me/did/{did:web identifier}"
```

## Create

### Generate Keypair

[Create your DID controller keypair](../did-controller-keypair.md)

Encode the public key using Base64 URL encoding.

```bash
cat pubkey | tail -c +13 | base58
# e.g. 7b4frLNZUy5SDnWJTuTCp34TcApYz2kDzBh6wkZudCu7
```

#### Create DID

Call the DID Create API to create a new `did:web` and return the associated DID document. Use the Base58 representation of the public key from previous step.

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

# e.g.
# {
#   "jobId": null,
#   "didState": {
#     "state": "finished",
#     "secret": null,
#     "didDocument": {
#       "verificationMethod": [
#         {
#           "id": "did:web:did-web.meeco.# me:7b4frLNZUy5SDnWJTuTCp34TcApYz2kDzBh6wkZudCu7#key-1",
#           "type": "Ed25519VerificationKey2018",
#           "publicKeyBase58": "7b4frLNZUy5SDnWJTuTCp34TcApYz2kDzBh6wkZudCu7"
#         }
#       ],
#       "service": [
#         {
#           "type": "LinkedDomains",
#           "serviceEndpoint": "meeco.me"
#         }
#       ],
#       "authentication": [
#         "did:web:did-web.meeco.me:7b4frLNZUy5SDnWJTuTCp34TcApYz2kDzBh6wkZudCu7#key-1"
#       ],
#       "assertionMethod": [
#         "did:web:did-web.meeco.me:7b4frLNZUy5SDnWJTuTCp34TcApYz2kDzBh6wkZudCu7#key-1"
#       ],
#       "id": "did:web:did-web.meeco.me:b4c63177-1ba3-48d2-8297-447c843db3d7"
#     },
#     "did": "did:web:did-web.meeco.me:b4c63177-1ba3-48d2-8297-447c843db3d7"
#   },
#   "didRegistrationMetadata": {
#     "duration": 175,
#     "method": "web"
#   },
#   "didDocumentMetadata": {}
# }
```

## Update

Call the DID Update API to update an existing `did:web` and return the associated DID document.

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
        "id": "did:web:did-web.meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1",
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
      "did:web:did-web.meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
    ],
    "assertionMethod": [
      "did:web:did-web.meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
    ]
  }
}'

# e.g.
# {
#   "jobId": null,
#   "didState": {
#     "state": "finished",
#     "didDocument": {
#       "id": "did:web:did-web.meeco.me:b4c63177-1ba3-48d2-8297-447c843db3d7",
#       "verificationMethod": [
#         {
#           "id": "did:web:did-web.meeco.# me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1",
#           "type": "Ed25519VerificationKey2018",
#           "publicKeyBase58": "Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE"
#         }
#       ],
#       "service": [
#         {
#           "type": "LinkedDomains",
#           "serviceEndpoint": "updated.example.com"
#         }
#       ],
#       "authentication": [
#         "did:web:did-web.meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
#       ],
#       "assertionMethod": [
#         "did:web:did-web.meeco.me:Fzn3cccoJqppxiNpPGfwNPaqNHubA3jM798wGzDNwTmE#key-1"
#       ]
#     }
#   },
#   "didRegistrationMetadata": {
#     "duration": 111,
#     "method": "web"
#   },
#   "didDocumentMetadata": {}
# }
```

## Deactivate

Call DID Deactivate API to deactivate an existing `did:web`.

```bash
curl -H "Authorization: Bearer TOKEN" \
     -X POST "https://identity-network-dev.meeco.me/did/deactivate?method=web" \
     -H "Content-Type: application/json" \
     -d '{
            "did": "{Replace_With_DID_WEB_Identifier}",
            "options": {
              "clientSecretMode": false
            }
        }'

```