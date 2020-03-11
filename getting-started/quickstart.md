# Quickstart

This guide aims to get you up and running and familiar with the API in about 15 minutes. We'll teach you how to setup your first user and how to create your first item.

### Get the Meeco CLI

Download the Meeco CLI and follow the installation instructions.

The first thing to do is setup an `.environment.yaml` file that allows you to connect to the sandbox environment.

{% code title=".environment.yaml" %}
```yaml
vault:
  url: https://api-sandbox.meeco.me
keystore:
  url: https://keystore-sandbox.meeco.me
  provider_api_key: 6af875150b33adce468620324c0c8852
downloader:
  url: https://downloader-sandbox.meeco.me
passphrase:
  url: https://passphrasestore-sandbox.meeco.me
```
{% endcode %}

### Create a User

Create your first user.

```bash
$ meeco users:create -p supersecretpassword > .user.yaml
```

The command above does a lot, if you want to learn what happens behind the scenes look at the guide about [Setting up Access](../guides/setting-up-access.md). The end result is captures in a file `.user.yaml` that captures the necessary information about the user that allows us to talk to the API in the next steps.

In next calls, you'll see an argument added `-a .user.yaml`

### Creating an Item

Items in the Meeco API require you to specify a template. This template can be seen as a contract with a number of predefined fields \(which we call slots\). To get all available templates execute the following command

```bash
$ meeco templates:list -a .user.yaml
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
$ meeco items:create-config vehicle -a .user.yaml > vehicle.yaml
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

Based on this configuration, we can create a new vehicle in the user digital vault.

```bash
$ meeco items:create -i vehicle.yaml -a .user.yaml
```

Congratulations, you have now created your first item in the vault.



