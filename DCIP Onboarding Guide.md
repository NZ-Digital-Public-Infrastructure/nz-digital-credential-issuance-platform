# Onboarding to the Digital Credential Issuance Platform
This page describes the process for New Zealand government agencies to start using the [Digital Credential Issuance Platform (DCIP)](/README.md).

![Flow chart of the agency onboarding process](/assets/Agency%20onboarding%20process.png "Agency onboarding process")
### Onboarding agreements
* When an agency intends to start developing a digital credential they contact the Digital Public Infrastructure (DPI) team at [issuance@gdda.govt.nz](mailto:issuance@gdda.govt.nz) to discuss the credential and delivery timelines.
* When the agency is ready to start development and testing they supply a [DPI Sandbox Access Form](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/sandbox/DPI%20Sandbox%20Access%20Form%202026-04-15.pdf) signed by the business owner, who should hold a General Manager or equivalent-level role.
* The DPI Sandbox Access Form provides details including:
  * Team members that should have access to the DCIP tenancy, and sandbox versions of the Govt.nz app and NZ Verify app.
  * Attributes that will be included in the credential.
  * The colour and logo of the credential to be displayed in the Govt.nz wallet.
* An Agency Access Agreement will be supplied by the DPI team for the credential business owner to sign. This is a detailed set of terms and conditions for participating in the DCIP. It includes details of costs to participate in the platform.

It is expected that the agency will be preparing their [Digital Identify Services Trust Framework (DISTF) accreditation application](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/information-for-providers)  for the credential in parallel with the technical development, including appointment of an [independent evaluator](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/information-for-providers/independent-evaluators) to review the areas of security, privacy, and identification management. 

### Technical setup
* The DPI team initiate a DCIP sandbox tenant with admininstrator access for selected agency technical team members.
* The sandbox tenancy uses automatically managed certificates.
* The sandbox tenancy can issue credentials into the sandbox version of the Govt.nz app wallet.
* If the credential is intended to be verifiable by the NZ Verify app in in-person situations, a verification template will be defined and loaded into the sandbox NZ Verify app to enable end-to-end testing.
* Once development and testing is completed and accreditation has been gained, or is in an advanced state in the application process, the production tenant will be created by the DPI team.
* Administrator access for the same agency users will be temporarily granted to allow the configuration to be set up, including creation of the API access credentials. 
* The DPI team will validate the tenant configuration, then will remove the agency administrator access.
* Production tenants use manually managed certificates, created in a secure offline Hardware Security Module (HSM) during Public Key Infrastructure (PKI) ceremonies.
* The DPI team will arrange the PKI ceremony and load the resulting certificates into the production tenant.
* The agency will be charged the production tenant onboarding fee.

### Go-live
* Once DISTF accreditation is granted, the production tenant will be activated.
* The root certificate for the tenant, the Issuer Authority Certificate Authority (IACA), will be added into the Digital Trust Register (DTS) Verified Issuer Certificate Authority List (VICAL) that holds all certificates used for accredited credentials. Relying parties use this VICAL to verify whether a credential comes from an accredited issuer.
* The IACA will be added to the trusted issuer list for the Govt.nz app wallet.
* The NZ Verify verification template will be activated in production, if applicable.
* The agency will do a full end-to-end smoke test in production, using the production API credentials to issue a credential into the Govt.nz app wallet and verify with the NZ Verify app or other relying party. This credential must then be revoked.
* Annual fees will begin being charged.





