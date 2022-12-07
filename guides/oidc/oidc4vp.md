# OpenID Connect for Verifiable Presentation

## Prerequisites

- [DID](dids/did-methods.md)
- [Presentation](../presentations.md)

## Who can use this?

Used by holder and verifier in a verification flow using the OpenID Connect protocol.

## Create Presentation Request



**Endpoint**

```bash
POST /oidc/presentations/requests
```

**Request**

*

**Responses**

The presentation request object that includes an unsigned JWT. The client calling this endpoint (e.g. holder wallet) is responsible for adding the signature.

## Update Presentation Request



**Endpoint**

```bash
PUT /oidc/presentations/requests/{id}
```

**Request**

* Request ID

**Responses**

The presentation request object.

## Read Presentation Request JWT

Public endpoint that returns the (signed) presentation request JWT.

**Endpoint**

```bash
GET /oidc/presentations/requests/{id}/jwt
```

**Request**

* Request ID

**Responses**

The presentation request JWT.

## Verify Presentation Request

Verify a (signed) presentation request JWT.

**Endpoint**

```bash
POST /oidc/presentations/requests/verify
```

**Request**

* Signed presentation request JWT

**Responses**

The result of the verification, either true or false.

## Create Presentation Response



**Endpoint**

```bash
POST /oidc/presentations/token
```

**Request**

*

**Responses**

The presentation response object that includes two unsigned JWT, `id_token` and `vp_token`. The client calling this endpoint (e.g. holder wallet) is responsible for adding the signatures for each token.

## Verify Presentation Response

Verify the presentation response to a given request. The steps performed are:

- Verify ID Token
- Verify VP Token
  - [Verify presentation](../presentations.md)
- Verify if the response is valid for the given request

**Endpoint**

```bash
POST /oidc/presentations/response/verify
```

**Request**

* Presentation Request JWT
* Signed ID Token
* Signed VP Token

**Responses**

The result of the verification, either true or false. In case of false, all errors are provided, with an explanation.