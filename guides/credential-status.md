# Credential status
Each [Issued Credential](issue-credentials.md) has a status attribute which entities can consult when verifying a credential. When an issuer no longer wants to underwrite the validity of a crendential, the issuer can revoke or suspend an [issued credential](issue-credentials.md). 
The property credentialStatus in the body of the VC is an URI to the statuslist, which is a publically available registry. The statusList maps the id of an issued credential to a credential that contains the status information (Status VC).

## Prerequisite
* StatusList2021
* [A Verifiable Credential Extension Registry](https://www.w3.org/TR/vc-data-model/#bib-vc-extension-registry) - containing available status schemes used in the verificaiton process
* [Issued Credentials](issue-credentials.md)

## Who can use this?
The issuer of the credential.

## Additional considerations
Every VC has a status attribute which has 'ACTIVE' as initial state. 
Suspending a VC is a temporary status, whereas revocation is irreversible.
The Verifiable Data Registry should be publically accessible. 

## Changing status

**Endpoint**
```bash
POST	/credentials/status
```

**Request**
* id - the id of the VC
* status - the new status as defined in the registry
* optional: statusReason - the reason why the status has changed

**Responses**
* A status VC is issued that proves the new status.
* The status list is updated with the new status VC id associated to the original VC id.

## Verifying status
The status of the VC is retrieved. If a status VC is issued, the signature of that status is verified.

**Endpoint**
```bash
Get	/credentials/status
```

**Request**
* id - the id of the VC

**Responses**

```bash
{
    "status": "ACTIVE",
    "isValid": true,
    "message": "string"
}
```
