---
description: Creating a keypair used to control a DID method
---

Create a new DID controller keypair using openssl. In this example, we use Ed25519, but other algorithms are also supported (see specification).

{% hint style="info" %}
Make sure you have openssl & GNU coreutils installed and avalible on command line (e.g. `brew install coreutils` on os x)
{% endhint %}

```bash
openssl genpkey -algorithm ed25519 -outform DER >privkey
openssl pkey -in privkey -pubout -out pubkey -inform DER -outform DER
```

This creates two files, `pubkey` and `privkey`.

Depending on the method, the public key is encoded in either

- Base64 URL (`base64url`)
- Base58 (`base58`)