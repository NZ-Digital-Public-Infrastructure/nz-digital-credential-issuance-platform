# The New Zealand Government Digital Credential Issuance Platform
## Purpose
The Digital Credential Issuance Platform (DCIP) is operated as part of the [Digital Public Infrastructure](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/README.md) (DPI) to provide a common capability for all New Zealand government agencies that issue digital credentials.
In the digital credentials ecosystem, the issuer is the participant responsible for creating and issuing a digital credential to a user. 

![Digital credential ecosystem diagram](/assets/ecosystem-diagram-issuance-highlight.png "Digital credential ecosystem diagram")

The DCIP allows government agencies to issue digital credentials to the public based on information held in their system(s) of record. The issuer is responsible for assuring the correctness of the information that goes into the credential and for ensuring that it is issued to the right person. The issuing agency must have gained [Digital Identify Services Trust Framework (DISTF)](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation) accreditation for the credential before it can be provided through the DCIP.  

The DCIP also provides capability for online verification of digital credentials.

## How does the DCIP work?
The DCIP issues follows the [OpenID for Verifiable Credential Issuance (OID4VCI)](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html) standard to issue credentials. Participating agency systems use the platform to create a credential offer for a user. The user's digital wallet on their mobile device receives the cryptographically signed credential that is:

* **Tamper-evident:** Any modification invalidates the signature.
* **Verifiable:** Any relying party can confirm authenticity against the issuer's certificate chain without contacting the issuer.
* **Portable:** Stored in the user's wallet and presentable across contexts.
* **Revocable:** Status can be updated after issuance.

The DISTF Draft Reference Architecture provides more details on the [digital credential trust model](https://github.com/nz-trust-framework/DISTF-reference-architecture/blob/main/7-TRUST.md), with particular reference to the New Zealand context.

## Who should use the DCIP?
Any New Zealand government agency that administers credentials can use the DCIP to provide these in digital format once DISTF accreditation has been obtained. Example types of credential include:  
* Personal identification
* Educational qualifications
* Health records
* Proof of role in a public register
* Licences 
* Financial information 
* Proof of address 
* Travel documents 
* Delegated authority 

## Using the DCIP
### Onboarding
Each issuing agency has access to a DCIP tenant configured with details of the credential to be issued. Some agencies may have more than one tenancy if they need to issue credentials in distinctly separate business areas. 

See our [DCIP Onboarding Guide](/DCIP%20Onboarding%20Guide.md) for full details of how to start using the DCIP.

Contact the DPI team at [issuance@gdda.govt.nz](mailto:issuance@gdda.govt.nz) to begin the onboarding process.

### Development
The agency business system that is responsible for mastering the credential data and verifying the identity of the credential recipient communicates with the DCIP tenant via an API for all aspects of credential management. 

See our [Digital Credentials Technical Guide](/Digital%20Credentials%20Technical%20Guide.md) for details of how to use the DCIP to issue and manage credentials.

### Testing

A sandbox test tenancy is provided for use during development and testing. This can be used in conjunction with sandbox versions of other DPI products, including the Govt.nz app wallet, and NZ Verify.

See our [DPI Sandbox Guide](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/sandbox/Sandbox%20User%20Guide.md) for details of the test products.

### Costs
There is an annual fee for use of the DCIP, using a tiered costing system proportional to the volume of potential credential recipients, to cover platform operational costs. This fee will start being charged when the production tenant is activated, or three months after the sandbox tenant is activated if production use has not yet begun.

There is a one-off fee for creation of the production tenant.

Fees are detailed in the Agency Access Agreement that will be provided prior to platform onboarding, and are reviewed on an annual basis.

### Support
The GDDA manages the DCIP and provides full technical and customer support including:
* Onboarding to the platform and related DPI ecosystem 
* Supporting issuers with technical troubleshooting 
* Maintaining published documentation 
* Management of the issuer's DCIP tenancy and related Public Key Infrastructure 
* Monitoring of platform performance 
* Co-ordinating communications regarding platform changes, maintenance, and incidents  
* Liaising with MATTR during operational incidents and problem tickets, at either the platform-level or for individual Issuing Agency tenancies 
* Prioritising feature enhancement requests 
* Reporting key statistics to issuers
* Managing the commercial agreement with the platform vendor to ensure delivery of agreed service levels

See our [DCIP Support Guide](/DCIP%20Support%20Guide.md) for details of Service Levels and support processes.

## Links
[Digital Public Infrastructure](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/README.md)

[Digital Identify Services Trust Framework (DISTF)](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation)

[DISTF Draft Reference Architecture](https://github.com/nz-trust-framework/DISTF-reference-architecture/blob/main/README.md)
