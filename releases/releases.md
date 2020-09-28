# Releases

## UNRELEASED October 2020, Release Notes

### Vault API v4.0.0

* On-sharing - the ability to allow other users to share an item that has been shared with you if the item’s owner allows it. This includes a verification step to make sure the on-sharer has not modified the data.
* Organizations - you can now create an account for your organisation and add members and services.
* Accepting of shares - we’ve identified that sometimes the terms of a share need to be reviewed and explicitly accepted or rejected before seeing the data so we’ve now made this possible.
* More efficient file attachments, we now have the ability to receive encrypted chunks of data so an entire file doesn’t have to be encrypted as one before sending it up.
* Improved Sharing - sharing is more streamlined, comprehensible and performant than ever before.
* Sharing of encrypted files with other users.
* Re-encrypting of shared data to cut down on the number of Encryption Keys needed to be stored/managed indefinitely.
* Updated to use latest BSON serialization format from Cryppo.

### KeyStore API v2.0.0

* No functional changes, just updated to use new linked libraries.

### SDK v1.0.0

* Added Item Update function
* Added Functions to Retrieve Client Tasks and Execute the Tasks.
* Added some Organization related commands where the logic was not just a simple endpoint req
* Exposed more methods from the user create and user login flow to allow more control from client applications.

### CLI v1.0.0

* Added Share Delete command.
* Added Item Update function
* Added Functions to Retrieve Client Tasks and Execute the Tasks.
* Added Commands for Creating and Managing Organizations, Organization Services, and Organization Members.
* Added Checking the Client Task Queue after updating an item to see if shares need to be updated.
* Added user login and logout methods.

### Cryppo/JS v1.0.0

* Updated to return null when an empty string is passed in instead of returning a serialized empty string.
* Updated to the latest underlying node-forge library.
* Update to use BSON serialization.
* Better handling of character encoding by forcing UTF-8 where appropriate.

### Cryppo/Ruby v1.0.0

* Update to use BSON serialization.
* Added Checking of the Client Task Queue after updating an item to see if shares need to be updated.

### Cryppo CLI v1.0.0

* No functional changes, just updated to use new linked libraries.

### File-Storage-Browser, File-Storage-Node v1.0.0

* Initial release of these browser-js/node-js packages to upload files as chunks of encrypted data.

### Style-Kit v1.0.0

* Initial release of this html/js library used for adding Meeco’s stylings to your app or components of your app.
