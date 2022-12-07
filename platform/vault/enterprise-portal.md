# Enterprise Vault

An Enterprise Vault is a secure service like an end-user Vault with added functionalities tailored to enterprises.

Enterprise refers to any entity, for example a company, a government, or a club. The term “Organisation” is used as the entity that controls the Enterprise Vault.

## Functionalities

The key functionalities of an Enterprise Vault are as follows:

- Items, Slots, Connections, Sharing, Classifications, Attachments as per an end-user Vault.
- The Organisation that manages an Enterprise Vault can have one or more Administrators (Admins), who are authorised to invite and manage other Admins and deploy Services. The (Organisation) Admins and Services all have access to Items in the Enterprise Vault.
- Admins can be assigned fine grained permissions (see ATOM > Security Rights), in line with the roles within the authorised Organisation, to allow different access rights to Items within the Vault.
- An Organisation can connect to any user or other organisation Vaults to provide services.
- Fined-grained consent to manage the sharing of data with end users.
- The Organisation Admin also has the ability to on-board third-party services, associated with the Organisation, to act on behalf of that Organisation and access Items, Connections and Shares in the Enterprise Vault.

## Services

Typical Services that are executed on behalf of an Enterprise Vault are:

Storing sensitive personal data that needs to be shared with other users. For example, verifiable credentials that are issued, or receiving verifiable credentials for verification purposes.

Securely sharing (structured) data in a persistent or one-off way. This data can be plain text, a document (e.g. verifiable credential) or an attachment (e.g. pdf). A specific implementation is the creation of an Item pushed via a secure API to an end-user Vault, without storing the item in the Enterprise Vault. This allows the secure sharing of sensitive data from other systems without the need to maintain the Item in the Enterprise Vault.

Securely receiving (structured) data from any authorised party (end-user or third party) within an Enterprise Vault ecosystem. This enables easy integrations with other services

## Management of the Enterprise Vault

An Enterprise Vault is managed via:

**Enterprise Portal** – The Enterprise Portal is an application that enables Organisation Admins to login and create other Admins, Item Schemas and Items, along with viewing Items created by the Organisation. It also provides a view of the end-users and other third-party services the Organisation is connected to, including the outgoing and incoming shares to and from these these parties.

**API** – All functionality is available using the SVX API to connect services.

**CLI** – Command line interface to interact with the Enterprise Vault.
