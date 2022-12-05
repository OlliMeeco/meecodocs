## Overview

### Introduction
The Meeco Enterprise Portal as an interactive interface to our set of APIs. Following an initial onboarding invitation, Tenant and Organisation Administrators are able to self-service in order to complete their desired workflows. Depending on their associated Security Rights, an Administrator will be able to undertake the following tasks:

* Manage their Tenant / Organisation
* Invite and manage other Administrators
* Utilise the Credentials Service to:

  * Create and manage credential schemas

  * Create and manage credential templates

  * Issue and revoke credentials

  * Create and manage verification templates

  * Create and manage verification requests

* Utilise the Enterprise Vault to:

* Create and manage connections

Create and manage items

Create and manage shares

Utilise Developer Tools and documentation

→ (link to docs for each of the points listed above).

 

Tenants
Role, Administrators & Activity
The Enterprise Portal enables companies to build and manage their own Tenancy. Once a Tenancy has been created, the Tenant Administrators can onboard Organisations and invite Organisation Administrators to join. Tenant Administrators have access to activity feeds, usage metrics and have the ability to create Credential Schemas. Once a Credential Schema is defined it can be made available to Organisations that will issue credentials. For more information on Credential Schemas see the Credential Schema tutorial.

Responsibilities
During onboarding, all users of the Enterprise Portal must agree to Meeco’s Terms and Conditions and Privacy Policy. It is the responsibility of the Tenant Administrator(s) to ensure the Organisations within their Tenancy are acting in an ethical, responsible manner.

 

Organisations
Role, Administrators & Activity
When Organisations are onboarded to the Enterprise Portal they are invited to join an existing Tenancy. As mentioned above, the Tenant governs the Tenancy that the Organisation conducts business in. Organisation Administrators manage the Organisation and, depending on their Security Rights, can action tasks associated with Credentials, an Enterprise Vault and Developer Tools. 

When utilising Credentials, Organisations identified as Issuers will be authorised to create Credential Templates and issue and revoke credentials. Those identified as Verifiers will be authorised to create Verification Templates, Verification requests and verify credentials. Each time an action is undertaken, Organisation Administrators are able to view and / or archive the transaction. This provides Organisations with fine-grained reporting and transaction management capabilities.

Responsibilities
During onboarding, all users of the Enterprise Portal must agree to Meeco’s Terms and Conditions and Privacy Policy. It is the responsibility of the Organisation Administrator(s) to ensure all Administrators are acting in an ethical, responsible manner. When handling personal identifiable information (PII) of end-users, Organisations are advised to follow Meeco’s Data Storage guideline.

 

Credentials
The Enterprise Portal provides access to credential management in a simple, user-friendly way. It enables non-technical users to engage with our Verifiable Credential (VC) API via an intuitive interface. Our Credential service utilises the W3C Verifiable Credentials Data Model to ensure standardisation and interoperability with  external systems. Additionally, when undertaking credential-based workflows via the Enterprise Portal, users are engaging in industry leading privacy and security practices that are aligned with current specifications and regulations, specifically the the Australian Privacy Principles and General Data Protection Regulation (GDPR) (for details see our Privacy and Security-by-design page).

Credential Schemas
The Enterprise Portal provides Tenant Administrators with the option to upload Credential Schemas to their Tenancy and make them available to Organisations. With access to a library of Credential Schemas, Organisations can specify which schema is required when creating Credential Templates and Verification Templates. When Organisations within the same ecosystem are referencing the same schemas, credential issuance and verification is accurate and standardised. For further details on how to create and manage Credential Schemas, see the Credential Schemas tutorial. 

Credential Templates
Organisations who have the authorisation to issue credentials (Issuers) can create Credential Templates which form the base of the credential types they will issue. Credential Templates include the associated credential schema (which determines the information required from the Holder), and credential styling information which enables customisation of credential colour schemes and logos. For further details on how to create a Credential Template, see the Credential Template tutorial. 

Issue credentials
The issuance of credentials is made easy via the Enterprise Portal by a simple step-by-step workflow. Issuers first select one of the Credential Templates they have created previously (see the Credential Template tutorial for details). The Credential Template defines the attributes that are required to be filled in. For example, a First Aid Certificate Credential Template might include attributes such as:

Student name

Student ID

Course ID

Subject code

Grade

Once the Organisation Administrator has filled in all required attributes specific to the credential recipient (Holder) they are able to Issue the credential. For further details on how to issue a credential, see the Issue / Revoke tutorial. 

Revoke credentials
When revoking a credential, an Organisation Administrator needs to simply locate the credential in the list of issued credentials, and select the option to revoke. For further details on how to revoke a credential, see the Issue / Revoke tutorial. 

Verification Templates
Organisations who have the authorisation to verify credentials (Verifiers) can create Verification Templates which form the base of Verification Requests. Verification Templates include the associated credential schema (which determines the information required from the Holder), and if required, a specific Issuer of these credentials can be defined. For further details on how to create a Verification Template, see the Verification Template tutorial. Please note that we acknowledge the term Verifiable Presentations as commonly used in credential specifications, but for the purposes of our Enterprise Portal, we refer to Verifiable Presentations as Verification Templates.

Verification Requests
In order to verify credentials via the Enterprise Portal, an Organisation Administrator simply creates a new Verification Request. Each request requires the Organisation Administrator to select a Verification Template which defines the credential(s) required from the Holder for verification. Once a Verification Template has been selected, the Organisation Administrator can view the request in the form of a QR Code. The QR Code can be presented to a Holder in-person, or downloaded and shared via any form of digital sharing method. For further details on how to create a Verification Requests, see the Verification Requests tutorial. 

 

Enterprise Vault
 

 

Additional thoughts…

Licencing and Agreements
While companies who manage their own Tenancy via Meeco’s Enterprise Portal will have a licencing agreement and contract directly with Meeco, it is the responsibility of the Tenant owner(s) to manage agreements with the Organisations they onboard to their Tenancy. 

 

Please note that throughout the Enterprise Portal Verifiable Presentations are referred to as Verification Templates. The reason for this is to ensure users who are not familiar with the W3C standard can undertake the workflow without encountering unfamiliar terminology that could affect the completion of their task.

 

Templates
Meeco’s Credential and Enterprise Vault components both utilise templates as a way for users to standardise how data is ordered and displayed.

