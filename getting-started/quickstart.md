# Quickstart

This guide aims to get you up and running and familiar with the API in about 15 minutes. We'll teach you how to setup your first user and how to create your first item.

## Get the Meeco CLI

Download the Meeco CLI and follow the installation instructions in the [Meeco CLI section](../tools/meeco-cli.md) of the documentation.

The first thing to do after setup is to create an `.environment.yaml` file that allows you to connect to the sandbox environment.

{% code title=".environment.yaml" %}
```yaml
vault:
  url: https://sandbox.meeco.me/vault
  subscription_key: DEV_PORTAL_SUBSCRIPTION_KEY
keystore:
  url: https://sandbox.meeco.me/keystore
  subscription_key: DEV_PORTAL_SUBSCRIPTION_KEY
downloader:
  url: https://sandbox.meeco.me/downloader
  subscription_key: DEV_PORTAL_SUBSCRIPTION_KEY
passphrase:
  url: https://sandbox.meeco.me/passphrasestore
  subscription_key: DEV_PORTAL_SUBSCRIPTION_KEY
```
{% endcode %}

## Create a User

Create your first user - let's call them "Alice".

```bash
$ meeco users:create -p supersecretpassword > .alice.yaml
```

The command above does a lot, if you want to learn what happens behind the scenes look at the guide about [Setting up Access](../guides/setting-up-access.md). The end result is captured in a file `.alice.yaml` that holds the necessary information about the user that allows us to talk to the API in the next steps.

In the next calls, you'll see an argument added with `-a .alice.yaml`

## Creating an Item

Items in the Meeco API require you to specify a template. This template can be seen as a contract with a number of predefined fields \(which we call slots\). To get all available templates execute the following command

```bash
$ meeco templates:list -a .alice.yaml
Fetching available templates... done
kind: Templates
spec:
  - passport_details
  - vehicle
  - bank_card
  - certificate_diploma
  - membership_subscription
  - pet
  - device
  - password
  - important_document
  - travel
  - services
  - medical
  - custom
```

Let's create a `vehicle` item. To prepare this we can run the create config command

```bash
$ meeco items:create-config vehicle -a .alice.yaml > vehicle.yaml
```

The next step is to edit the file to contain some data.

{% code title="vehicle.yml" %}
```bash
kind: Item
metadata:
  template: vehicle
spec:
  label: "DeLorean"
  slots:
    - name: purchase_date
      value: "01/01/1981"
    - name: type
      value: "Sports Car"
    - name: vin
      value: "123456789"
    - name: licence_plate
      value: "1AAA999"
    - name: model_make
      value: "DMC DeLorean"
    - name: power
      value: "132 PS"
```
{% endcode %}

Based on this configuration, we can create a new vehicle in Alice's digital vault.

```bash
$ meeco items:create -i vehicle.yaml -a .alice.yaml
```

Congratulations, you have now created your first item in the _**Vault**_.

From here you can either keep learning about the Meeco Platform and tools, or jump into the [Connections and Sharing tutorial](../guides/connections-and-sharing.md) to keep using the CLI to make a connection with another user and share the item you created above. 

