# Releases

## Feb 2021, Release Notes

### CLI v2.0.0

- `users:get` renamed to `users:login`, has the effect of recreating tokens if expired.
- `users:get` now returns user's id and other user info.
- `shares:create-config` takes connection and item config files (output of `connections:create` and `items:create`) instead of the respective ids.
- `client-task-queue:list` no longer accepts `--suppressChangingState`, pass `--update` if you want to set listed tasks to `in_progress`.
- `client-task-queue:run-batch` can now run `failed` tasks too.
- new command `client-task-queue:update` allows changing status of client tasks.
- `client-task-queue` commands now have `--limit` parameter
- added `items:create-thumbnail`
- `meeco items:list` allow filtering list by `templateId`, `scheme`, `classification` and `sharedWith`.
- added `oidc_token` OIDC token header support for authenticating user.

### SDK v2.0.0

- Major revamp of Services API:
  - All custom service methods specify the required credentials by interfaces
  - Services provide their base APIs via the `getAPI` method
  - Services with paginated responses offer a `listAll` method
- Added Service for delegating child users
- Added cryppo wrapper classes `SymmetricKey` and `PublicKey` to simplify key usage within SDK
- Added classes for manipulating Items and Slots: `DecryptedItem` for client-side copies, `NewItem` and `ItemUpdate` for pending changes.
- Added demo app for sharing and delegation
- added `oidc_token` OIDC token header support for authenticating user.

### Vault API v19.3.0

- Add user_public_key to response of `GET /invitations/{:token}`
- Remove `child_public_key_for_login` parameter from `POST /child_users`
- Update description of `sharing_mode` in `POST /items/{id}/shares`
- New field `item_shared_via_another_share_id` in ShareWithItemData If this field is NULL it means that the rendered item has been created via the currently displayed share. If `item_shared_via_another_share_id` is not NULL, it means that the rendered item has been created via a different share, and the ID if the share is in `item_shared_via_another_share_id`. The client is advised to re-run the call with this ID.
- Change default behaviour of `GET /client-task-queue` to not change task states `supress_changing_state` parameter has been changed to change_state to reflect the change.
- Add delegation_token parameter description in `POST /invitations`
- Change behavour of GET /client_task_queue
  - Will now return items of all states by default
  - Accepts a list of states instead of a single state as before
  - Generated TypeScript client will now require that states are passed using the generated ClientTaskQueueGetStateEnum enum
- Removed logging in with a password `POST /session/login`
- Add item_ids query parameter for `GET /outgoing_shares` as search filter.
- Support for OIDC2
- Added filter target_id to `GET /client_task_queue`: `GET /client_task_queue?target_id=id`
- Add `GET /event_feed endpoint`
- Swagger models `Slot`, `Attachment`, and `DirectAttachment` all have a new field: `attachment_uid`. The function of `attachment_uid` is to signal whether the actual file referenced from two different attachments is the same or not. In other words, when an attachment is shared to a different user, a new attachment record is created with a new id, but `attachment_uid` stays the same. This ID cannot be used to retrieve the attachment.
- added filter `classification_node_names` to `GET /items: GET /items?classification_node_names=value_1,value_2,value_3`
- added filter `shared_with` to `GET /items: GET /items?shared_with=user_id`. If present, only items will be fetched which have been shared with the given user. Works for items owned by the current user as well as for items owned by someone else and on-shared by the current user.

### KeyStore API v5.1.0

- Add `POST /child_users` endpoint
- Add `POST /delegations` endpoint
- Add `GET`, `DELETE /delegations/:delegation_token` endpoint
- Add `PUT /delegations/:delegation_token/share` endpoint
- Add `PUT /delegations/:delegation_token/reencrypt` endpoint
- Rename `DELETE /session/all` :delete_all_session_params query param to :except_current of boolean type.
- Add `POST /delegations/:delegation_token/claim` endpoint
- Added user delegation header `Meeco-Delegation-Id`. `Meeco-Delegation-Id` contains the ID of a user that the current user has delegation permissions for. If delegation has been set up correctly, the current user of the action will be the user in `Meeco-Delegation-Id`
- Remove encryption spaces endpoints
- Dropped support for `POST /keypairs/external_id/:external_id/` decrypt_session_token
- Support for OIDC2
- External_identitiers in Keypair is a string, not an object.
- Private_dek_external_id is added to the response of `POST /child_users`

### Cryppo/JS v2.0.0

- Breaking changes: https://github.com/Meeco/cryppo-js/blob/master/README.md
  - Encrypt method now accept bytes UInt8Array as input.
  - Decrypt method now produce bytes UInt8Array as output.
  - RSA signature also accept bytes UInt8Array as input.
  - Private key encryption also accepts bytes as input.

### File-Storage-Browser, File-Storage-Node v3.0.0

- Updated cryppo version 2.0.0
- Updated Vault-api-sdk: 19.X.X & keystore-api-sdk: 5.X.X

### Cryppo CLI v2.0.0

- No functional changes, updated new cryppo version 2.0.0

## October 2020, Release Notes

### Vault API v16.0.1

- On-sharing - the ability to allow other users to share an item that has been shared with you if the item’s owner allows it. This includes a verification step to make sure the on-sharer has not modified the data.
- Organizations - you can now create an account for your organisation and add members and services.
- Accepting of shares - we’ve identified that sometimes the terms of a share need to be reviewed and explicitly accepted or rejected before seeing the data so we’ve now made this possible.
- More efficient file attachments, we now have the ability to receive encrypted chunks of data so an entire file doesn’t have to be encrypted as one before sending it up.
- Improved Sharing - sharing is more streamlined, comprehensible and performant than ever before.
- Sharing of encrypted files with other users.
- Re-encrypting of shared data to cut down on the number of Encryption Keys needed to be stored/managed indefinitely.
- Updated to use latest BSON serialization format from Cryppo.

### KeyStore API v3.0.0

- Remove the shared key endpoints as sharing keys for shares will now happen in the vault.
- Removed the encryption_spaces endpoints in favor of just using the Date Encryption Key endpoints.

### SDK v1.0.0

- Added Item Update function
- Added Functions to Retrieve Client Tasks and Execute the Tasks.
- Added some Organization related commands where the logic was not just a simple endpoint req
- Exposed more methods from the user create and user login flow to allow more control from client applications.

### CLI v1.0.0

- Added Share Delete command.
- Added Item Update function
- Added Functions to Retrieve Client Tasks and Execute the Tasks.
- Added Commands for Creating and Managing Organizations, Organization Services, and Organization Members.
- Added Checking the Client Task Queue after updating an item to see if shares need to be updated.
- Added user login and logout methods.

### Cryppo/JS v1.0.0

- Updated to return null when an empty string is passed in instead of returning a serialized empty string.
- Updated to the latest underlying node-forge library.
- Update to use BSON serialization.
- Better handling of character encoding by forcing UTF-8 where appropriate.

### Cryppo/Ruby v1.0.0

- Update to use BSON serialization.
- Added Checking of the Client Task Queue after updating an item to see if shares need to be updated.

### Cryppo CLI v1.0.1

- No functional changes, just updated to use new linked libraries.

### File-Storage-Browser, File-Storage-Node v2.0.0

- Initial release of these browser-js/node-js packages to upload files as chunks of encrypted data.

### Style-Kit v1.0.0

- Initial release of this html/js library used for adding Meeco’s stylings to your app or components of your app.
