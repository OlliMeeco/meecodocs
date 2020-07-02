# Meeco SDK & CLI

We have created the [Meeco SDK](https://www.npmjs.com/package/@meeco/sdk) and the [Meeco CLI](https://github.com/Meeco/js-sdk/tree/master/packages/cli).

## SDK

{% embed url="https://github.com/Meeco/js-sdk/tree/master/packages/sdk" %}

Our first release of the SDK is written in Typescript, and we have it released as an [NPM Package here](https://www.npmjs.com/package/@meeco/sdk) so you can easily import our modules into your project and start making calls to our APIs.

For full usage and functionality, you can visit the [API Docs](https://meeco.github.io/js-sdk/).

### SDK in other languages

Whilst the SDK is currently only available in Typescript/Javascript, we are actively creating the SDK in other languages:

* Java
* .NET
* Swift
* Python
* Ruby

If you would like a port of the Meeco SDK in another language, let us know through the [Meeco.me contact page](https://www.meeco.me/contact) with "Developer Portal" set as the Enquiry Type.

## CLI

### A tool for interacting with Meeco services and databases

{% embed url="https://github.com/Meeco/js-sdk/tree/master/packages/cli" %}

To get the Meeco platform up and running so that you can get a feel for it as a developer, we have created some tools that set up a Meeco User and create all the necessary access tokens and keys for you to explore what Meeco can do.

In short, it can:

* Create Users
* Connect Users
* Create Items and Templates
* Share Items between Connected Users

You can use the keys and tokens that the Meeco CLI has generated in combination with the [Cryppo-CLI](cryppo.md) tool to get a feel for the encryption and decryption flows.

Feed this data back to the Meeco-CLI to store it against a Meeco User and then Share it! You can read more about [Sharing and Connections](../guides/connections-and-sharing-1.md) and [Items](../guides/items-and-slots.md) in the Guides section of this documentation site.

The Typescript CLI and SDK Github repos reside below in the downloads section.

The CLI documentation can be found below - it will help you set up tool so you can begin making requests against our APIs.
{% embed url="https://github.com/Meeco/js-sdk/tree/master/packages/cli" %}

The Typescript SDK documentation can be found below
{% embed url="https://github.com/Meeco/js-sdk/tree/master/packages/sdk" %}

The CLI relies heavily on the SDK, so by running through the commands we've got set up, you'll be able to follow along and learn how the Meeco Platform works, and give you some idea about what it can do.

### What Next?

Now, go back to the [Meeco Quickstart](../getting-started/quickstart.md) guide and follow the run-through in order to create a User, setup the Keystore, gain access to the Vault and create your first Item!
