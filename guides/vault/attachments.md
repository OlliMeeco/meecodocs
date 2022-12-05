# Attachments

## Attaching a File to an Item

Every `item` in the meeco vault has the capability of having multiple files attached to it. The attachments are always attached
to the `item` via a `slot` with the `slot_type` `attachment`. Assuming you have created an item already (such as the one you may
have created in the getting-started page) and have the `.item.config` file still, lets create another item with that same config so as not to conflict with other steps later on in this guide.

```bash
meeco items:create -i .item-config.yaml -a .alice.yaml > .item2.yaml
```

the next step is to create an `attachment-config.yaml` file with the following content.

```yaml
kind: FileAttachment
metadata:
  item_id: e8670e6c-8a95-43ff-a8d1-08805f612250 # (target item id from .item2.yaml)
spec:
  label: 'Secret test webm video'
  file: './test.webm'
```

Then run the cli command

```bash
meeco items:attach-file -c attachment-config.yaml -a .alice.yaml > .attach-response.yaml
```

You will get a response in the `attach-response.yaml` file that looks like the following

```yaml
attachment:
  id: fe9ef5ad-b29c-4b71-bb73-b14fd4b88dca
  content_type: video/webm
  filename: test.webm
  ...
slots:
  - id: 67b2a8c2-4a4b-4a53-9b2f-5411cd63576b
    attachment_id: fe9ef5ad-b29c-4b71-bb73-b14fd4b88dca
    slot_type_name: attachment
    item_id: e8670e6c-8a95-43ff-a8d1-08805f612250
    label: My Secret File
    encrypted_value: Aes256Gcm.z-T6OnEB6ssmkQK4RcvtYHjh2rE5PregqflhZoVXq6w=.QUAAAAAFaXYADAAAAAAhvEHoJqo845AsORoFYXQAEAAAAACahlEdh5rJcKfnl0DtTiaBAmFkAAUAAABub25lAAA=
    ...
item:
  id: e8670e6c-8a95-43ff-a8d1-08805f612250
  label: Item Label
  slot_ids:
    - 67b2a8c2-4a4b-4a53-9b2f-5411cd63576b
  ...
```

Note: The slot's `encrypted_value` in this case is a new encryption key which has been encrypted with the user's own private data
encryption key. The reason the file gets encrypted with a new data encryption key instead of directly with the user's existing
private data encryption key has to do with sharing. By using this method when sharing, instead of having to re-encrypt the whole file
with another data encryption key the slot's `encrypted_value` can simply be decrypted then re-encrypted with the data encryption key
used for sharing.

## Downloading the Attached File

To download an attached file the CLI needs to know the item's id and the slot's id, this is so the CLI can decrypt the data encryption
key from the encrypted_value of the slot (as mentioned above). Both of these values can be found in the `.attach-response.yaml` file under `slots[0].id` and `slots[0].item_id`.

To download run the following

```bash
meeco items:get-attachment e8670e6c-8a95-43ff-a8d1-08805f612250 67b2a8c2-4a4b-4a53-9b2f-5411cd63576b -o ./output/ -a .alice.yaml
# meeco items:get-attachment <item id> <slot id> -o <file download path> -a <authorization>
```

## Sharing and receiving the attachment

Sharing an attachment simply works the same way you would share any other slot. First you will need to have a connection to another
user, see the directions in the "Connections and Sharing" page to set up a connection first. Assuming you have a connection set up
already and have the second user's info in a .bob.yaml file...

Run the command

```bash
meeco shares:create-config -i .item2.yaml -f .alice.yaml -c .connection.yaml > share-config2.yaml
# meeco shares:create-config -i <item id> -f .alice.yaml -c <connection id> > share-config.yaml
```

To create the share config then to create the share itself

```bash
meeco shares:create -c share-config2.yaml > .create-shares-response.yaml
```

You should see some output like the following in the `.create-shares-response.yaml` file.

```yaml
shares:
  - id: d9b68c36-110f-4171-8d9c-6bd580eff32d
    owner_id: c1f2485d-fe8d-4de2-b45f-deee52931207
    sender_id: c1f2485d-fe8d-4de2-b45f-deee52931207
    recipient_id: ca14e3ae-d7c9-49fe-85ec-ac3306414803
    public_key: "..."
    encrypted_dek: Rsa4096.H-V2A_GlAbA3InFwKbdPoDVheM0p7kDIGg7tAtlnrF9-CFHtpo7pgE7MKBoszEp5jAkKwOlffZvaYt0ustjKb3yKDB-VKSKdZgu8yCkfJVNe8tgs5JpoZqg41krVrhVcUTLz6AsSfEXhnlFwKWLgbghqa7ad3u6LIGVVOTs_6-SBeuyJaYHDDBEN_TTiVqbIE7TU6LIUFSp38rpPOc0AM15FGZWhWcupYsy5gSO_jAOneBNi-sie392LX1LDPYbXi5fn-MSsWDektrR4bN0WlXA0iptTC-YqIrOFif9DFHL5qD5fis4Hfee95FCCPLBEtNoPNqU5u6YcE1a2XVlwPTMmeOVYDhHzl0HvT63QVc-zxhHqs3Tcg1mZtgDNb55qbUtNF8IGA1oOjG8LD69eIYOR3aO-cUs-iZcsZ-H0E7IqwX-bdCvZlLzUP1KI5sO3tIj32d9dCUCkvIJDf0TmPvB9UmF1rdoGDkT2dGvyGMA2sFQDhURq3I-NIOi4kp85h3l3JRN0BPcW1VzYCwX4Cn0HhG2brojv_Z8-j1QpCmOI9NO9XzJiMNi1ACMv-mJaEY4cBxvKtviY3eNaLsn8u-YrzH2InEOqrX7V9M2ynajf2YdJWxqCxUMXF_vWHxK04C6EQB2tdQ7SVNFchdjsuAjX-ue_RGmZ0hMNOXFCYjg=.QQUAAAAA
    terms: null
    expires_at: null
    ...
```

to see the incoming shared item first make a request for items as bob run

```bash
meeco items:list -a .bob.yaml
```

you will see something like the following as output

```yaml
kind: Items
spec:
  - id: fce07d6a-0c6d-4615-acef-46beee08bb5f
    share_id: d9b68c36-110f-4171-8d9c-6bd580eff32d
    own: false
    name: item_label
    label: Item Label
    slot_ids:
      - 9ddaa9f7-b928-4727-a0ee-6dfb487ca0c8
    me: false
    background_color: null
    original_id: e8670e6c-8a95-43ff-a8d1-08805f612250
    owner_id: c1f2485d-fe8d-4de2-b45f-deee52931207
    ...
```

Notice how the `share_id` matches the share output from the previous command. We can then request the item itself.

```bash
meeco items:get fce07d6a-0c6d-4615-acef-46beee08bb5f -a .bob.yaml
# meeco items:get <item id> -a .bob.yaml
```

Returning

```yaml
kind: Item
spec:
  id: fce07d6a-0c6d-4615-acef-46beee08bb5f
  own: false
  label: Item Label
  slot_ids:
    - 9ddaa9f7-b928-4727-a0ee-6dfb487ca0c8
  original_id: e8670e6c-8a95-43ff-a8d1-08805f612250
  owner_id: c1f2485d-fe8d-4de2-b45f-deee52931207
  share_id: d9b68c36-110f-4171-8d9c-6bd580eff32d
  ...
  slots:
    - id: 9ddaa9f7-b928-4727-a0ee-6dfb487ca0c8
      own: false
      share_id: d9b68c36-110f-4171-8d9c-6bd580eff32d
      attachment_id: 4ac61cad-7911-46e9-a9f8-659b1eb79fc6
      item_id: fce07d6a-0c6d-4615-acef-46beee08bb5f
      encrypted_value: Aes256Gcm.FYteKXIcTkjnC4OpqVcNFSzu5xwI3Eol0IubZUDpOhk=.QUAAAAAFaXYADAAAAACRE4YnWzELWDMfmE0FYXQAEAAAAACJtfJh93-EI7igsedpZ39aAmFkAAUAAABub25lAAA=
      label: My Secret File
      original_id: 67b2a8c2-4a4b-4a53-9b2f-5411cd63576b
      value: "´\x10¹(MKnCÈ«BêSD\x01¾]V*Ç\x16qøÀ¡^ó"
      ...
  thumbnails: []
  attachments:
    - id: 4ac61cad-7911-46e9-a9f8-659b1eb79fc6
      content_type: video/webm
      filename: test.webm
      ...
```

Now we have all the information we need to be able to download the attached file

```bash
meeco items:get-attachment fce07d6a-0c6d-4615-acef-46beee08bb5f 9ddaa9f7-b928-4727-a0ee-6dfb487ca0c8 -a .bob.yaml -o ./output/
# meeco items:get-attachment <item id> <slot id> -a <user auth> -o <file download output directory>
```

## Using the file-storage-browser or file-storage-node Packages

The above flow is a great start to understanding how everything works however for actual product implementation these npm packages
are likely to be more useful so please head to the following links and check out the README.md files there.  

file-storage-browser  
<https://github.com/Meeco/js-sdk/tree/releases/file-storage/latest/packages/file-storage-browser>  

file-storage-node  
<https://github.com/Meeco/js-sdk/tree/releases/file-storage/latest/packages/file-storage-node>  

