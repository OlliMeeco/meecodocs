# OpenID Connect for Verifiable Presentation

List of endpoints that assist holder wallet and verifier to participate in the [OpenID for Verifiable Presentations](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html) protocol. Built on top of OAuth 2.0, it allows a client (wallet) to present claims in the form of [W3C Verifiable Credentials](https://www.w3.org/TR/vc-data-model/).

This implementation currently supports credentials and presentations in JWT format (vc-jwt, vp-jwt).
## Prerequisites

- [DID](dids/did-methods.md)
- [Presentation](../presentations.md)

## Who can use this?

Used by organisations (verifiers) and users (holders) in a verification flow using the OpenID Connect protocol.

## Create Presentation Requests

Creation of a presentation request.

**Endpoint**

```bash
POST /oidc/presentations/requests
```

**Request**

* Organisation (header, optional)

**Responses**

The presentation request object that includes an unsigned JWT. The client calling this endpoint (e.g. verifier system) is responsible for adding the signature.

## Update Presentation Request

Update an existing presentation request by ID.

**Endpoint**

```bash
PUT /oidc/presentations/requests/{id}
```

**Request**

* Request ID
* Organisation (header, optional)

**Responses**

The updated presentation request object.

## Read Presentation Request JWT

Public endpoint that returns the (signed) presentation request JWT.

**Endpoint**

```bash
GET /oidc/presentations/requests/{id}/jwt
```

**Request**

* Request ID

**Responses**

Signed presentation request JWT token.

## Verify Presentation Request

Verification of the SIOP token. The steps performed during this verification are:

- Resolve verifier DID
- Verify request signature
- Extract the presentation definition uri
  - Verify presentation definition structure

**Endpoint**

```bash
POST /oidc/presentations/requests/verify
```

**Request**

* Signed presentation request JWT

**Responses**

The result of the verification, either true or false.

## Create Presentation Response

Generate id_token for request submission based on the Wallet information and the verifiable presentation token

**Endpoint**

```bash
POST /oidc/presentations/token
```

**Request**

* Presentation Request JWT

**Responses**

The presentation response object that includes two unsigned JWT, `id_token` and `vp_token`. The client calling this endpoint (e.g. holder wallet) is responsible for adding the signatures for each token.

## Verify Presentation Response

Verify the presentation response to a given request. The steps performed are:

- Verify ID Token
- Verify VP Token
  - [Verify presentation](../presentations.md)
- Verify if the response is valid for the given request, i.e. if it matches the presentation definition from the request

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