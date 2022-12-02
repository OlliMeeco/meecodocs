---
description: Resolving & dereferencing supported DID methods
---

## DID Resolution

DID resolution, also referred to as the "Read" operation, is a function that takes a DID (and some metadata) as input and returns a DID document (and some metadata) as output.

Resolution is performed as defined in the [DID Core](https://www.w3.org/TR/did-core/) and [DID Resolution](https://w3c-ccg.github.io/did-resolution/) specifications. The implementation is based on the [Universal Resolver](https://github.com/decentralized-identity/universal-resolver) project.

Supported output is

- DID document and metadata in JSON-LD
- DID document in JSON-LD
- DID document in CBOR

```bash
curl -H "Authorization: Bearer b082c420-df67-4b06-899c-b7c51d75fba0" \
     -X GET "https://svx-api.meeco.me/did/did:sov:WRfXPg8dantKVubE3HX8pw"
```

```bash
curl -H "Authorization: Bearer b082c420-df67-4b06-899c-b7c51d75fba0" \
     -H "Accept: application/did+ld+json" \
     -X GET "https://svx-api.meeco.me/did/did:sov:WRfXPg8dantKVubE3HX8pw"
```

```bash
curl -H "Authorization: Bearer b082c420-df67-4b06-899c-b7c51d75fba0" \
     -H "Accept: application/did+cbor" \
     -X GET "https://svx-api.meeco.me/did/did:sov:WRfXPg8dantKVubE3HX8pw"
```

## DID URL Dereferencing

DID URL dereferencing is a function that takes a DID url as input and returns either (1) a DID document, (2) a resource within the DID document or (3) a resource external to the DID document.

Using a fragment to fetch

```bash
curl -H "Authorization: Bearer b082c420-df67-4b06-899c-b7c51d75fba0" \
     -H "Accept: application/did+ld+json" \
     -X GET "https://svx-api.meeco.me/did/did:sov:WRfXPg8dantKVubE3HX8pw#key1"
```
