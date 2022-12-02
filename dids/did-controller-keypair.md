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

## FAQ

### How do I install `base58` app (on OS X).
There are two ways to install it. If one app doesn't work, try the other one.

```bash
# using howbrew
brew install base58
# using cargo (requires rust tooling to be installed)
cargo install bs58-cli
## hint latter works best by symlinking: ln -s source_path target_in_path/base58
```