# Introduction
Within the SVX Platform, credential schemas are used to form the basis of a verifiable credential. They structure a set of claims about a Holder and determine the logic behind how the claims are used. Without credential schemas, verifiable credentials would not be able to be issued.


# What you need before starting
The schema file (typically a JSON Schema)


# Additional considerations
Each JSON Schema consists of the following mandatory attributes:

* type (e.g. JsonSchemaValidator2018). The specific type definition determines the content of each data schema.

* id (a URI that identifies the schema file)

Credential schema upload is available to Tenant Administrators and must comply with the following rules:

* A plain JSON object.

* The structure is later checked based on the specification: JSON Schema

All the attributes in the example are required, but values could be different, e.g. URL address of Schema, name, description etc.



### Example credential JSON schema

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
## Create Credential Schema

**Endpoint**

```bash
POST /schemas
```

**Request**

* Name – name of the credential schema
* JSON Schema
* List organisations

**Responses**

The credential schema object that is created.

## View Credential Schemas

Retrieve a list of credential schemas.

**Request**

```bash
GET /schemas
 ```

**Request**

* Organisation (header)

**Responses**

* List of credential schema objects

The endpoint works for a Tenant and Organisation user based on the X-Meeco-Organisation-ID header.

Returns a list of schemas available to the caller.

In the context of an organisation, entering organization_ids `organization_ids` list always contains only one item - caller organisation ID.



## Associate Organisations with Credential Schemas
### Credential Template
When an Organisation Administrator creates a Credential Template, they will need to select the required Credential Schema. In this case, we would need to fetch data using the following API call:

**Endpoint**

```bash
GET /schemas/{id}
```
**Request**

* ID of Credential Schema
* Organisation (header)

**Responses**

* Selected Credential Schema

### Issue Credentila

We need to use the same endpoint during Issue Credential process, because based on the properties of the associated Credential Schema, we will need to fill in the information for the Credential. 

**Requests**

```bash
GET /schemas/{id} - fetch the selected credential schema for credential
 ```

## Edit Credential Schemas
We need to use the following endpoint for editing Credential Schema:

**Endpoint:**

```bash
PUT /schemas/{id} - apply all changes
 ```
**Request**

* ID of Credential Schema

**Responses**

* Edited Credential Schema (only name or available organisations can be edited)


## Archive Credential Schemas

It is possible for a Tenant Administrator to archive Credential Schemas, but if the schema is used in other operations such as issuing a credential or forming a verification template, the schema can be flagged as archived:true.

**Endpoint**

```bash
DELETE /schemas/{id}
```
**Request**

* ID of Credential Schema

**Responses**

* Credential Schema deleted

## Retrieve Credential Schema definition 

**Endpoint**

```bash
GET /schemas/{id}/{version}/schema.json
```

**Request**

* ID of Credential Schema
* version

**Responses**

* Schema json retrived
