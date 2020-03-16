# What is the API-of-Me

The getting started section is our primary source for people new to the platform. Everyone should be able to follow the [Quickstart](getting-started/quickstart.md) guide as it teaches the basics. Next step is to read the guides available in the _Guides_ section and we highly recommend following along with the code samples that we provide.

In the guides, the following topics are covered

* Introduction to the data model and terminology
* Setting up a user with the necessary encryption artefacts
* Creating an item and filling it with data
* Learn the place of classifications in the system
* Make a connection with another user and share an item

Before diving in however, we'll start with an overview of the different components within Meeco. One more note, if you want to follow along, you'll want to sign up for the [free API keys](https://).

## Platform Overview

The Meeco platform or API-of-Me as we like to call it is a technology that is designed for everyone to store data and control how data is shared with or consent is given to other people or organisations that they trust.

At no point is information stored or accessed in plaintext. It is only the client who can access this information inside a trusted execution environment.

To enable this, Meeco provides a number of services that can be used in unison to provide a smooth experience.

* **Vault** – securely store and share data from your vault. Manage connections and relationships to others.
* **Keystore** – store and retrieve keys used in the different encryption tasks.
* **Downloader** – facilitates downloading and sharing of attachments.

Note that it is possible to pick and choose any of the solutions above if you happen to have a solution in place already. For example, the Vault can be used without the Keystore if you already have another key management component in place.

## **API Keys**

All requests to the API must contain an API key, which you can request [for free](https://dev.meeco.me).

### **Environments**

Currently, we have the following environments setup:

* **sandbox**: allows you to experiment freely in an environment that is always updated to include the latest and the greatest endpoints and structures.

### **Services**

Services can be reached by appending one of the following to `https://sandbox.meeco.me`:

* `/vault`: Vault API
* `/keystore`: Keystore API
* `/downloader`: Download Engine API

### Tools

Meeco has made available for use some tools to make using the API-of-Me easier.

The Meeco CLI is available [here](https://github.com/Meeco/cli). Open the [Meeco CLI](getting-started/meeco-cli.md) section in this documentation for detailed installation instructions.

We've also created an encryption library called `Cryppo` that makes using the encryption and decryption routines we recommend much easier. We've created a Javascript and Ruby library - the examples contained within this guide will focus on the JS version. Check out `Cryppo-JS` [here](https://github.com/Meeco/cryppo-js) and `Cryppo` with Ruby [here](https://github.com/Meeco/cryppo).

