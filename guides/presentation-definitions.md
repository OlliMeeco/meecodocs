# Presentation Definition

Presentation Definitions define which credential(s) a Verifier requests and for what purpose. Which credentials is defined by the credential schema and the issuer. The resulting object is conformant with [W3C Presentation Exchange 1.0](https://identity.foundation/presentation-exchange/spec/v1.0.0/) specification and serves as input to a [Verification Request](oidc4vp.md).

## Prerequisites

* [Verifiable Credential JSON Schema](credential-schemas.md)
* [Issuer DID](dids/did-methods.md) (optional)

## Who can use this?

Presentation definiton is created by an organisation.

## Create Presentation Definition

Creation of a presentation definitions for an organisation.

**Endpoint**

```bash
POST /presentation_definitions
```
**Request**

* Organisation (header)
* Name
* Purpose
* List of required credentials. For each, the following is defined
  * Name
  * Purpose
  * Verifiable Credential JSON Schema URL
  * Issuer DID

**Response**

The presentation definition object that is created.
Created Presentation Definition which associated with the Credential Schema and relates to the organisation which initiate creation of presentation definition

## Read Presentation Definitions

### List

Retrieve a list of presentation definitions owned by an organisation.

**Endpoint**

```bash
GET /presentation_definitions
```
**Request**

* Organisation (header)
* Filters (optional):
  * Status

**Response**

List of presentation definitons in this organisation.

### One Object

Retrieve a presentation definition by ID. The resulting object needs to be owned by the organisation that is making the request.

**Endpoint**

```bash
POST /presentation_definitions/{id}
```
**Request**

* Presentation Definition ID
* Organisation (header)

**Response**

A presentation definition object.

## Archive Presentation Definition

Presentation definition can be archived and restored, and status of it affects to the ability of using this presentation definition in the future flow, such as beeing added to verification request.

**Endpoint**

```bash
PUT /presentation_definitions/{id}
```
**Request**

* Presentation Definition ID
* Organisation (header)

**Response**

Updated Presentation Definition Archive status for organisation, `is_archived: true` for archieved definitions, and `is_archived: false` for active or restored.

## Read Presentation Definition JSON

Public endpoint that returns the JSON representation of presentation definition, following the [W3C Presentation Exchange 1.0](https://identity.foundation/presentation-exchange/spec/v1.0.0/) specification.

**Endpoint**

```bash
GET /presentation_definitions/{id}/definition.json
```
**Request**

* Presentation Definition ID
* Organisation (header)

**Response**

Returns JSON schema for a presentation definition
