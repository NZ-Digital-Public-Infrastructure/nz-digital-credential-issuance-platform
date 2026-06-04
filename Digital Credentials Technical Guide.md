# Digital Credential Issuance Platform Technical Guide
This page describes technical details and configuration requirements for issuing agencies onboarding to the Digital Public Infrastructure (DPI) [Digital Credential Issuance Platform (DCIP)](/README.md).

## Background
The DCIP is hosted on the [MATTR VII](https://learn.mattr.global/docs/issuance) platform. The platform provides a portal for users with administrator access to set up tenant and credential configuration details. An API provides full credential management functionality for the issuing agency to access from their system of record.

For technical implementation detail and more general background on digital credentials and the OpenID4VCI standard, refer to [MATTR Learn](https://learn.mattr.global/docs/issuance). 

This page provides the implementation requirements for New Zealand Government agencies using the DCIP:

* [DCIP Tenant Configurations](#dcip-tenant-configurations)
  * [Custom Domain](#custom-domain)
  * [Document Signer Certificates & Status List Signer Certificates ](#document-signer-certificates--status-list-signer-certificates)
  * [Certificate and credential validity periods](#certificate-and-credential-validity-periods)
* [Credential Requirements](#credential-requirements)
  * [Format](#format)
  * [Claims Persistence](#claims-persistence)
* [Managing Digital Credentials](#managing-digital-credentials)
  * [API](#api)
  * [Webhooks](#webhooks)
  * [Status Lists](#status-lists)
  * [Issuing into the Govt.nz App Wallet](#issuing-into-the-govtnz-app-wallet)

## DCIP Tenant Configurations
### Custom Domain
Tenancies on the DCIP use the naming convention: 

    agency-tenant.vii.nz01.mattr.global 

Issuing agencies must create and manage a publicly resolvable domain that will forward to the production platform tenant. This may optionally be done in the sandbox tenant. All API calls to the tenant and status lists must use the agency’s domain name, not the platform domain. 

The preferred convention is an issuance subdomain under the agency's domain. The DNS record must point to the agency's DPI issuance tenant, for example: 

    issuance.agency.govt.nz → agency-tenant.vii.nz01.mattr.global 

This domain must be set up prior to the creation of the production certificate for the tenant. 

Note that this domain is used for the public credential status list and is embedded in every credential at issuance, so cannot be changed without reissuing credentials. 

### Document Signer Certificates & Status List Signer Certificates 

Every mdoc credential is cryptographically signed using a Document Signer Certificate (DSC). Credential revocation status is signed using a Status List Signer Certificate (SLSC). Both chain up to an Issuing Authority Certificate Authority (IACA). 

On the DCIP, certificates for production tenants are manually managed. The IACA is generated and held offline in a Hardware Security Module (HSM) under DPI control, the private key is never stored or managed by MATTR VII. This is the recommended approach under ISO/IEC 18013-5 for high-assurance credential issuance, and meets the requirements of the National Cyber Security Centre (NCSC). 

**How this works in practice**

Sandbox test tenant certificates are automatically managed by the DCIP. Production tenants follows a CSR-based flow: 
* DPI registers the IACA (root CA) with the MATTR VII tenant in PEM format 
* MATTR VII generates a private key internally (stored in its KMS) and produces a Certificate Signing Request (CSR)
* DPI uses the offline HSM to sign the CSR against the IACA private key, producing the DSC or SLSC 
* The signed certificate is uploaded to MATTR VII and validated 
* Once validated, the signer and IACA are activated and can be used for credential and status list signing 

DPI is responsible for the full lifecycle of the IACA and signer certificates, including renewal and revocation. MATTR VII does not automatically manage this. 
 
### Certificate and credential validity periods 

> [!NOTE]
> **Credentials issued through the DCIP have a maximum validity period of one year.**
 
To ensure that all active issued credentials are associated with an active certificate, the following periods are used by the DPI team. 

| Certificate | Validity period | Operational usage period |
| ----------- | ---------------:| ------------------------:|
| IACA        |         5 years |                3.5 years | 
| DSC         |        820 days |                 731 days |
| SLSC        |        820 days |                 731 days | 

DPI manages certificate renewal internally along with MATTR, with sufficient lead time built in before expiry. Agencies do not need to manage certificate lifecycle directly. 

## Credential Requirements
Refer to [MATTR Learn - credential configuration](https://learn.mattr.global/docs/issuance/credential-configuration/overview) and [MATTR Learn - structure to function](https://learn.mattr.global/docs/concepts/mdocs/structure-to-function) for details of how to set up credential configurations in the DCIP.

### Issuance Workflows
The DCIP supports the two distinct issuance workflows described in the [OpenID4VCI standard]((https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)). Agencies may choose to use either or both of these methods, depending on the requirements of their service and users:

**Authorisation Code flow:** The user initiates the flow from the [Govt.nz app wallet](https://github.com/NZ-Digital-Public-Infrastructure/govt-nz-app-wallet) begin an authentication process with the issuing agency. After the user successfully completes authentication and identification processes, the issuer's authentication provider returns an authorisation code. The wallet then exchanges this code for an access token, which is used to obtain the credential.

Refer to [MATTR Authorization Code flow](https://learn.mattr.global/docs/issuance/authorization-code/overview) for implementation details.

**Pre-authorised Code flow:** The issuing agency completes authentication and identification of the user before providing a credential offer. The offer could be in the form of a QR code to be scanned by the user's wallet or a link that opens the offer in the user's wallet. The user does not need to authenticate again and the wallet presents the pre-authorised code to retrieve an access token and then claim the credential. 

Refer to [MATTR Pre-authorized Code flow](https://learn.mattr.global/docs/issuance/pre-authorized-code/overview) for implementation details.

> [!IMPORTANT]
> For additional security when generating a Pre-Authorised Code flow QR code, the issuer must separately share a six-digit [transaction code](https://learn.mattr.global/docs/issuance/pre-authorized-code/journey-pattern#transaction-code) with the user. This code is generated by the DCIP when the `transactionCodeConfiguration` parameter is used in the credential offer API call. The user will be prompted to enter the code in their wallet to claim the credential.

### Format
Credentials issued through the DCIP must be in mdoc format, based on the ISO/IEC 18013-5 and ISO/IEC 23220 standards. 

Every mdoc has a DocType - a unique string identifier that tells verifiers the kind of credential they are receiving. For mobile driver licence (mDL) credentials this is standardised as `org.iso.18013.5.1.mDL`. Photo IDs use `org.iso.23220.photoid.1`. For other credential types, agencies define their own DocType. 

Claims within an mdoc are grouped into namespaces to prevent naming collisions. The `org.iso.23220.1` namespace provides a set of data elements to express personal attributes for describing a natural or legal person (e.g. given_name, birth_date, portrait). These data elements can be used in combination to create a wide range of credentials utilising common attributes. These data elements are detailed in Table 2 of ISO/IEC 23220-4.  

Custom namespaces can be added for agency-specific claims. Namespace design must align with the draft DISTF Reference Architecture. Refer to the [Hierarchy of Namespaces](https://github.com/nz-trust-framework/DISTF-reference-architecture/blob/main/9-DATA.md#91-hierarchy-of-namespaces) for guidance on how namespaces should be structured and named within the New Zealand trust framework.

### Claims Persistence
MATTR VII provides a `claimsToPersist` configuration option that allows claim data to be stored against a user object in the MATTR VII database, making it available for future credential issuance operations. 

> [!IMPORTANT]
> **Agencies must not use `claimsToPersist`.**

This is a firm requirement under the DCIP Privacy Impact Assessment (PIA) and credential accreditation obligations. Persisting credential subject data within the MATTR VII tenant creates unnecessary data residency and privacy risk that is inconsistent with these obligations. **By default, no claims are persisted, and this must be maintained.**

## Managing Digital Credentials
### API
Agency systems interact with the DCIP using the [MATTR VII API](https://learn.mattr.global/docs/api-reference). This API has a broad range of functions, generally only a few of these operations are required for credential issuance and maintenance. 

Agencies will create API client ID and secret details to access the API for generating credential offers and managing credential statuses.

### Webhooks
The API is used to generate a credential offer to a user. The agency system that initiated the credential offer must support [webhooks](https://learn.mattr.global/docs/platform-management/webhooks-overview) to receive notification when the user has accepted the offer and a credential has been issued. This will enable the capture and storage of a `credentialId` value that can be used to revoke a credential through the use of public status lists.

### Status Lists
The DCIP supports credential revocation via publicly hosted status lists. MATTR VII automatically creates and manages the lists as credentials are issued.  Agencies must support the use of the credential status update API operation to change the status of an issued credential. 

Status lists use a 1-bit format per the current [Token Status List specification (Draft 14)](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-status-list-14), where each issued credential occupies a single bit: 0 for valid, 1 for invalid. Each list can hold the status of up to 500,000 individual credentials. The DCIP manages the creation of additional lists when required. 

> [!IMPORTANT]
> There must be a means to revoke credentials issued through the DCIP, mandated by Rule 83 of the [DISTF rules](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation/trust-framework-rules).

To revoke a credential, the `credentialId` generated at issuance is required. The recommended approach for capturing this is to configure a MATTR VII webhook that notifies the agency's backend when a credential is issued. Refer to the [revocation](https://learn.mattr.global/docs/issuance/revocation/overview) section of the MATTR documentation for full details.

### Issuing into the Govt.nz App Wallet
When generating a credential offer, the API will return the offer URI using the `openid-credential-offer://` scheme for maximum interoperability. The URI will allow the credential to be issued into any OpenID4VCI wallet.

To issue specifically into the [Govt.nz app wallet](https://github.com/NZ-Digital-Public-Infrastructure/govt-nz-app-wallet), this URI must be transformed before being provided to the recipient.

**Sandbox**  

Replace `openid-credential-offer://` with `https://sandbox.m.app.govt.nz/openid-credential-offer` 

**Production**  

Replace `openid-credential-offer://` with `https://m.app.govt.nz/openid-credential-offer`

For example, a credential offer would look like: 

    https://sandbox.m.app.govt.nz/openid-credential-offer?credential_offer=%7B%22credential_issuer… 

## References
[Digital Credential Concepts](https://learn.mattr.global/docs/concepts)

[MATTR VII Issuance](https://learn.mattr.global/docs/issuance)

[MATTR VII API](https://learn.mattr.global/docs/api-reference)

[Open ID for Verifiable Credential Issuance (OpenID4VCI)](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)

[Digital Identity Services Trust Framework Rules](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation/trust-framework-rules)

