# Introduction
Within the SVX Platform, credential schemas are used to form the basis of a verifiable credential. They structure a set of claims about a Holder and determine the logic behind how the claims are used. Without credential schemas, verifiable credentials would not be able to be issued. 

 
# What you need before starting
The schema file (typically a JSON Schema)


# Additional considerations
Each Credential Schema consists of the following mandatory attributes:

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
## Create schemas
**Request**

```bash
POST/schemas
```
**Example Payload**
```bash
{
  "schema": {
    "name": "Schema name",
    "schema_json": {
      "$schema": "https://json-schema.org/draft/2019-09/schema",
      "description": "Basic schema description",
      "name": "Basic schema name",
      "type": "object",
      "properties": {
        "id": {
          "type": "string"
        },
        "name": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "name"
      ],
      "additionalProperties": false
    },
    "organization_ids": [
      "9322c384-fd8e-4a13-80cd-1cbd1ef95ba8",
      "986dcaf4-c1ea-4218-b6b4-e4fd95a3c28e"
    ]
  }
}
```
**Responses**

```bash
201 - Schema created
400 - Error + see the explanations in console 
 ```

## View Credential Schemas
All created schemas are stored in a list and can be retrieved using the API.

**Request**

```bash
GET /schemas
 ```

The endpoint works for a Tenant and Organisation user based on the X-Meeco-Organisation-ID header.

Returns a list of schemas available to the caller.

In the context of an organisation, entering organization_ids `organization_ids` list always contains only one item - caller organisation ID.

 

## Associate Organisations with Credential Schemas
When an Organisation Administrator creates a Credential Template, they will need to select the required Credential Schema. In this case, we would need to fetch data using the following API call:

**Request**

```bash
GET /schemas/{id}
```
The same happens when an Organisation Administrator issues credentials:

**Requests**

```bash
GET /schemas - list in dropdown is opened
GET /schemas/{id} - fetch the selected schema for this credential
 ```

## Edit Credential Schemas
When a Tenant Administrator edits a Credential Schema they need to perform the following steps:

**Requests:**

```bash
GET /schemas - Open page with lits of schemas
GET /schemas/{id} - open particular schemas
PUT /schemas/{id} - apply all changes
 ```

## Archive Credential Schemas
It is possible for a Tenant Administrator to archive Credential Schemas, but if the schema is used in other operations such as issuing a credential or forming a verification template, the schema can be flagged as archived:true.

**Request**

```bash
DELETE /schemas/{id}
```
