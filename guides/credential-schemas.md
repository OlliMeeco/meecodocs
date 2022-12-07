# Credential Schemas

Credential schemas are used to define the structure for the claims of a verifiable credential. It uses the [Data Schema](https://www.w3.org/TR/vc-data-model/#data-schemas) property of a credential. Schemas use the [Verifiable Credentials JSON Schema 2022](https://w3c-ccg.github.io/vc-json-schemas/) specification, published by the W3C Credentials WG.

Using schemas allows tenants, organisations and users to

- Agree on the structure of data and facilitate data exchange
- Extract information from the schema

It is possible to create a credential without specifying a schema via the API, but the Enterprise Portal makes this step mandatory. In the portal, the schema is used to render an input form when creating a credential via the UI.

A credential schema, once published, is versioned and made available via a public URL. This allows anyone to validate it at any point in time.

## Prerequisites

- Verifiable Credential JSON Schema

### Verifiable Credential JSON Schema

Each JSON Schema consists of the following mandatory attributes:

- type (e.g. JsonSchemaValidator2018). The specific type definition determines the content of each data schema.
- id (a URI that identifies the schema file)
Credential schema upload is available to Tenant Administrators and must comply with the following rules:
- A plain JSON object.
- The structure is later checked based on the specification: JSON Schema

All the attributes in the example are required, but values could be different, e.g. URL address of Schema, name, description etc.

Below is an example JSON schema.

```bash
{
  "$schema": "https://json-schema.org/draft/2019-09/schema",
  "name": "Example",
  "description": "Example",
  "type": "object",
  "properties": {

    "id": {
      "type": "string",
    }
  },
  "required": ["id"],
  "additionalProperties": false,
}
```

## Who can use this?

Credential schemas are managed by a tenant administrator. They assign it to an organisation.

An organisation can list the credential schemas that are assigned to them.

Anyone can read the JSON schema (via a separate endpoint) that is part of the credential schema object.

## Create Credential Schema

Creation of a credential schema.

**Endpoint**

```bash
POST /schemas
```

**Request**

* Name – name of the credential schema
* JSON Schema - JSON schema file
* List of organisations - organisations where this credential schema can be used (optional)

**Responses**

The credential schema object that is created. Upon creation, version `1.0` is assigned.

## Read Credential Schemas

Retrieve a list of credential schemas.

**Request**

```bash
GET /schemas
 ```

**Request**

* Organisation (header)

**Responses**

List of credential schema objects available to the user.

In the context of an organisation, the `organization_ids` attribute contains only one item - caller organisation ID.

## Update Credential Schema

Update an existing credential schema by ID.

Note that in this version, the schema cannot be updated.

**Endpoint:**

```bash
PUT /schemas/{id}
 ```
**Request**

* ID of Credential Schema
* Name – name of the credential schema
* List of organisations - organisations where this credential schema can be used (optional)

**Responses**

The updated credential schema object.

## Read Verifiable Credential JSON Schema

Public endpoint that returns the JSON schema file. No authentication necessary.

**Endpoint**

```bash
GET /schemas/{id}/{version}/schema.json
```

**Request**

* Id – ID of the Credential Schema
* Version – Version of the Credential Schema

**Responses**

Returns JSON schema for a credential schema
