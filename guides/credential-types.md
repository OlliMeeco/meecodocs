# Credential Types

Credential types are used to link a [Credential Schema](credential-schemas.md) to an Organisation and define the visual appearance for a credential issued by that organisation. The credential type is used in the Meeco Portal and Meeco Wallet to give a credential of that particular schema and that particlar organisation its unique look and feel.

## Prerequisites

- [Credential Schema](credential-schemas.md)

## Who can use this?

Credential types are managed by an organisation administrator.

## Create Credential Type

Creation of a credential type.

**Endpoint**

```bash
POST /credential_types
```

**Request**

* Name – name of the credential type
* Credential Schema - credential schema this type is applicable for
* Style
  * Text Colour
  * Background Colour – Background colour for the credential (CSS styles supported)
  * Logo – Logo displayed in the top left corner of the credential

**Responses**

The credential type object that is created.
