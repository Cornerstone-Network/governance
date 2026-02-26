# Cornerstone ID – Credential Governance

## 1. About this Document

This document describes the **Cornerstone ID** verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes real estate professionals, lenders, insurers, financial service providers, and any relying parties requiring verified person-level identity.

The Cornerstone ID Credential is issued by the **Cornerstone Network** to all users on the platform—homeowners, professionals, advisors, service providers, and trust network members. It represents **foundational person-level identity only**, with no role-specific, property-specific, or employment-specific attributes.

**Cornerstone ID is the base credential that everyone always has**. Users leverage their Cornerstone ID to obtain additional credentials (Home Credential, Professional Credential, Accreditation Credential, Portfolio Issuer Credential) which then enable access to application-layer features. This credential-based access control architecture allows users to hold multiple credential types simultaneously, with applications dynamically provisioning features based on which credentials users possess.

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **0.9**   | 26-Feb-2026 | JSON-LD format finalization; removed AnonCreds-specific attributes (`birthdate_dateint`, `fsa_code`); removed Email+Phone OTP verification path; updated schema hosting; added design rationale and industry references | Mathieu Glaude |
| **0.1**   | TBA         | Initial release                     | Mathieu Glaude |

## 2. Credential Overview

The Cornerstone ID Credential is a verifiable credential (VC) issued to individuals who have completed identity verification through trusted sources. It enables the holder to prove their verified identity while sharing only the minimum data required.

This credential serves as the **identity foundation** for the Cornerstone network:
- **Everyone receives a Cornerstone ID** upon successful identity verification
- **Required before any network participation**: Users must hold a Cornerstone ID before they can receive other credentials or access network features
- **Foundation credential**: This is the base layer that all other credentials reference via the `cornerstone_id` attribute
- **No role or persona information** is included in this credential
- **Supports credential-based access control**: Applications query which credentials users hold to provision appropriate features

Each issuance is backed by verification from trusted identity sources:
- **Interac Bank Verification Service** (Canada-wide), or
- **BC Person Credential** (British Columbia)

The credential is issued directly into the **Cornerstone Wallet** and can be consumed by relying parties without requiring them to re-run identity checks.

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Cornerstone ID                                            |
| **Schema:**     | Cornerstone ID v0.9                                       |
| **Issuer:**     | Cornerstone Network                                       |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`) |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                      |

### 2.1 Attribute Summary (8 Attributes)

| **#** | **Name**            | **Attribute**          | **Data Type**   | **Required** | **Notes** |
|-------|---------------------|------------------------|-----------------|--------------|-----------|
| 001   | Given Names         | `given_names`          | String          | Yes          | Legal given names (may include middle names). |
| 002   | Family Name         | `family_name`          | String          | Yes          | Legal family/surname. |
| 003   | Date of Birth       | `birthdate`            | String (YYYY-MM-DD) | Yes      | ISO 8601 date format. |
| 004   | Verified Email      | `verified_email`       | String          | Yes          | Verified via OTP during onboarding. |
| 005   | Verified Phone      | `verified_phone`       | String          | Yes          | Verified via Interac or SMS OTP. |
| 006   | Postal Address      | `postal_address`       | JSON object     | No           | Residential/mailing address from identity source. |
| 007   | Cornerstone User ID | `cornerstone_user_id`  | String (UUID)   | Yes          | Platform-generated opaque identifier. |
| 008   | Identity Evidence   | `identity_evidence`    | String / URI    | Yes          | UUID referencing IDV source and stored evidence record. |

### 2.2 Design Rationale

**Attribute decisions in this version:**

- **`birthdate` (ISO 8601 date string)** replaces the previous `birthdate_dateint` (integer YYYYMMDD). The integer format existed to support AnonCreds-style ZK predicate proofs (e.g., "prove age > 19 without revealing DOB"). Since this credential ecosystem uses W3C JSON-LD format exclusively, selective disclosure is handled via BBS+ data integrity cryptosuites rather than integer predicates. ISO 8601 dates are the standard for JSON-LD credentials.

- **`fsa_code` removed.** The Forward Sortation Area (first 3 characters of postal code) existed to enable AnonCreds ZK proofs for geographic predicates without revealing full address. In a JSON-LD format with BBS+ selective disclosure, verifiers can request only `postal_address` or derive FSA from the postal code when needed. Including a derived field as a separate attribute added no value in this format.

- **`postal_address` retained as optional.** Not every user is a homeowner, and their residential address may differ from properties they own. For professionals and other non-homeowner users, having a verified address in the Cornerstone ID is useful to the ecosystem even when they hold no Home Credentials.

- **Email+Phone OTP verification path removed.** Only two high-assurance verification sources are accepted: Interac Bank Verification (Canada-wide) and BC Person Credential (British Columbia). This simplifies the assurance model and eliminates the need to distinguish between verification tiers.

**Industry references:**

- **vLEI (GLEIF)**: The most mature production credential ecosystem uses minimal foundational identity credentials. Their Official Organizational Role (OOR) credential carries only 4 core attributes (`LEI`, `personLegalName`, `officialRole`, `dt`). The principle is that a foundation credential should be tight, immutable, and contain only what it uniquely attests to.

- **W3C Verifiable Credentials Data Model**: Credentials use standard JSON-LD contexts and ISO 8601 date formats as specified by the W3C VC Data Model.

## 3. Credential Details

### 3.1 Issuer

The Cornerstone ID Credential is issued by the **Cornerstone Network**, acting as the credential authority. Each credential is issued only after identity verification is successfully completed through at least one trusted source.

### 3.2 Schema and Credential Definition Governance

The Cornerstone ID Credential schema and definition are managed by the Cornerstone Network and registered on the **Cornerstone Trust Registry**. Updates to the schema follow a change-managed governance process to ensure interoperability across all applications accessing the Cornerstone network.

### 3.3 Issuer Data Source

The data comes from trusted identity verification sources:

- **Interac Bank Verification Service**: High-assurance identity verification through banking relationships (Canada-wide)
- **BC Person Credential**: Provincial digital identity credential (British Columbia)

In both cases, email verification (OTP) and optionally phone verification (SMS OTP) are performed during the credential issuance process.

**Out of Scope**: This credential does NOT include or reference:
- Property ownership or homeownership data
- Professional licensing or employment data
- Any role or persona indicators (homeowner, professional, etc.)

#### 3.3.1 Data Updates
- A credential reflects the state of identity verification at issuance.
- Identity changes (name change, address change) require revocation and re-issuance.
- Evidence is retained for five years in compliance with regulatory requirements.

### 3.4 Assurance

The credential provides person-level identity assurance through verification from trusted sources:
- **Interac Bank Verification**: High-assurance identity verification meeting regulatory requirements (Canada-wide)
- **BC Person Credential**: High-assurance provincial digital identity credential (British Columbia)

**Note**: No assurance level indicator is included in the credential itself. Relying parties evaluate assurance based on the evidence array, which documents verification sources and methods.

### 3.5 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**, supporting both `revocation` (permanent) and `suspension` (reversible) purposes. Relying parties check credential status before accepting the credential.

### 3.6 Revocation

A Cornerstone ID Credential will be revoked in cases such as:
1. User account deletion or closure
2. Fraud or identity mismatch detected
3. Identity information changes (name change, requires re-issuance)
4. Regulatory or legal request
5. User requests credential refresh

**Cascade Revocation**: Since the Cornerstone ID is the foundation credential referenced by all others, its revocation has cascading effects. When a Cornerstone ID is revoked, all credentials that reference it via `cornerstone_id` should be reviewed for revocation. Note that credentials are independent — there are no hard technical dependencies enforced at the credential level — but operationally, Cornerstone Network manages cascade revocation as a platform-level process.

Re-issuance involves re-verification through a trusted identity source and issuance of a new credential to the holder's Cornerstone Wallet.

## 4. Credential Definition

### 4.1 Credential Schema

The Cornerstone ID Credential conforms to W3C Verifiable Credentials (JSON-LD) and uses a Cornerstone Network-managed schema:

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/cornerstone-id.json` *(exact URL TBD)*
- **Schema Versioning:** Semantic versioning. Breaking changes produce a new major version.
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/cornerstone-id-v0.9.json` *(exact URL TBD)*

### 4.2 Subject of the Credential

The subject is the **individual holder**, bound to their Cornerstone platform account. Binding is confirmed by successful identity verification through at least one trusted source and association with a DID generated by the user's wallet.

### 4.3 Attributes

#### 4.3.1 Core Identity Attributes (Required)

*Given Names (001)*

<table>
  <tr><th>Attribute</th><td><code>given_names</code></td></tr>
  <tr><th>Description</th><td>Verified legal given names (may include middle names).</td></tr>
  <tr><th>Source</th><td>Interac <code>given_name</code> + <code>middle_name</code>, or BC Person <code>given_names</code>.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>John Michael</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Family Name (002)*

<table>
  <tr><th>Attribute</th><td><code>family_name</code></td></tr>
  <tr><th>Description</th><td>Verified legal family name.</td></tr>
  <tr><th>Source</th><td>Interac or BC Person.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Smith</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Date of Birth (003)*

<table>
  <tr><th>Attribute</th><td><code>birthdate</code></td></tr>
  <tr><th>Description</th><td>Date of birth in ISO 8601 format (YYYY-MM-DD).</td></tr>
  <tr><th>Source</th><td>Interac or BC Person.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Examples</th><td><code>1985-06-21</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Design Note</th><td>Changed from <code>birthdate_dateint</code> (integer YYYYMMDD) which was AnonCreds-specific. JSON-LD credentials use standard ISO 8601 dates; selective disclosure is handled via BBS+ signatures.</td></tr>
</table>

*Verified Email (004)*

<table>
  <tr><th>Attribute</th><td><code>verified_email</code></td></tr>
  <tr><th>Description</th><td>Email verified via OTP during Cornerstone onboarding process.</td></tr>
  <tr><th>Source</th><td>Cornerstone Onboarding Flow.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>john.smith@example.com</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Verified Phone (005)*

<table>
  <tr><th>Attribute</th><td><code>verified_phone</code></td></tr>
  <tr><th>Description</th><td>Verified phone number. From Interac <code>phone_number</code> when available; otherwise verified via SMS OTP.</td></tr>
  <tr><th>Source</th><td>Interac or SMS verification.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>+1-604-555-0123</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Cornerstone User ID (007)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_user_id</code></td></tr>
  <tr><th>Description</th><td>A platform-generated opaque identifier linking the VC to the Cornerstone user record. This identifier does NOT encode any role or persona information.</td></tr>
  <tr><th>Source</th><td>Cornerstone platform user management system.</td></tr>
  <tr><th>Data Type</th><td>String (UUID format)</td></tr>
  <tr><th>Examples</th><td><code>550e8400-e29b-41d4-a716-446655440000</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.2 Optional Attributes

*Postal Address (006)*

<table>
  <tr><th>Attribute</th><td><code>postal_address</code></td></tr>
  <tr><th>Description</th><td>Normalized postal address. Include ONLY if present in the source identity provider (Interac or BC Person). This is NOT property ownership address; it is the person's mailing/residential address from identity verification.</td></tr>
  <tr><th>Source</th><td>Interac or BC Person (if available).</td></tr>
  <tr><th>Data Type</th><td>JSON object containing: <code>street_address</code>, <code>locality</code>, <code>region</code>, <code>postal_code</code>, <code>country</code></td></tr>
  <tr><th>Examples</th><td><code>{"street_address": "123 Main St", "locality": "Vancouver", "region": "BC", "postal_code": "V6B 1A1", "country": "CA"}</code></td></tr>
  <tr><th>Required</th><td>No - only if provided by identity source</td></tr>
  <tr><th>Design Note</th><td>Retained because not every user is a homeowner. For professionals and other non-homeowner users, a verified residential address is useful to the ecosystem even when they hold no Home Credentials.</td></tr>
</table>

#### 4.3.3 Evidence Attributes

*Identity Evidence (008)*

<table>
  <tr><th>Attribute</th><td><code>identity_evidence</code></td></tr>
  <tr><th>Description</th><td>UUID referencing IDV source(s), author, and date of verification. Links to stored evidence record for audit and compliance purposes.</td></tr>
  <tr><th>Source</th><td>Cornerstone IDV process (Interac or BC Person verification).</td></tr>
  <tr><th>Data Type</th><td>String (UUID) or URI</td></tr>
  <tr><th>Examples</th><td><code>urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Notes</th><td>Evidence retained for five years in compliance with regulatory requirements. Full evidence details available in the <code>evidence</code> array of the credential.</td></tr>
</table>

### 4.4 Removed Attributes (v0.9)

The following attributes were present in earlier drafts and have been removed:

| Attribute | Reason for Removal |
|-----------|-------------------|
| `birthdate_dateint` (Integer) | Replaced by `birthdate` (ISO 8601 string). The integer format was AnonCreds-specific for ZK predicate proofs. JSON-LD credentials use standard date formats; selective disclosure is handled via BBS+ data integrity cryptosuites. |
| `fsa_code` (String) | Existed for AnonCreds ZK geographic predicates. In JSON-LD with BBS+ selective disclosure, verifiers can derive FSA from `postal_address` when needed. A derived field as a separate attribute adds no value in this format. |

## 5. Evidence Requirements

The `evidence` section of the credential documents the identity verification sources used. Each identity verification source must appear as an object in the `evidence` array.

### 5.1 Evidence Array Structure

Each evidence object must include:

<table>
  <tr><th>Field</th><th>Description</th><th>Required</th></tr>
  <tr><td><code>type</code></td><td>Type of evidence (e.g., "DocumentVerification", "IdentityProofing")</td><td>Yes</td></tr>
  <tr><td><code>method</code></td><td>Verification method used (e.g., "InteracBankVerification", "BCPersonCredential")</td><td>Yes</td></tr>
  <tr><td><code>verificationDate</code></td><td>ISO 8601 timestamp of when verification occurred</td><td>Yes</td></tr>
  <tr><td><code>matchFields</code></td><td>Array of fields that were verified (e.g., ["name", "dob", "account"])</td><td>Yes</td></tr>
  <tr><td><code>recordLocator</code></td><td>Reference to stored evidence record (UUID or URI)</td><td>Yes</td></tr>
  <tr><td><code>verifier</code></td><td>Entity that performed verification (e.g., "Interac Corp.", "Province of BC")</td><td>Yes</td></tr>
</table>

### 5.2 What Goes in Evidence

- Type of verification performed
- Method/source used (Interac, BC Person)
- Verification timestamp
- Fields that were matched/verified
- Reference locator for audit trail
- Verifier entity information
- Any metadata required for regulatory auditability (e.g., FINTRAC compliance)

### 5.3 What Must NOT Go in Evidence

- **No assurance level indicators** (e.g., HIGH, MODERATE, LOW) - relying parties infer assurance from verification sources
- **No role or persona indicators** (homeowner, professional, etc.)
- **No references to property or employment verification sources**
- **No derived predicates or analysis**

### 5.4 Example Evidence Object

```json
{
  "evidence": [
    {
      "type": "IdentityProofing",
      "method": "InteracBankVerification",
      "verificationDate": "2026-01-15T14:32:00Z",
      "matchFields": ["given_name", "middle_name", "family_name", "birthdate", "phone_number"],
      "recordLocator": "urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "verifier": "Interac Corp."
    }
  ]
}
```

## 6. Issuance Rules

### 6.1 When Credential is Issued

The Cornerstone ID Credential is issued when:
1. User completes identity verification through at least one trusted source (Interac Bank Verification or BC Person Credential)
2. User has generated a DID in their Cornerstone Wallet
3. Identity verification successfully completes (name, date of birth, contact information verified)

**No property or employment verification is required** for Cornerstone ID issuance. This is purely person-level identity verification.

### 6.2 Credential Binding

The credential is bound to:
- A DID generated by the user's wallet (stored in `credentialSubject.id`)
- A Cornerstone user account (via `cornerstone_user_id`)

### 6.3 Multi-Source Verification

If a user verifies through multiple sources (e.g., Interac AND BC Person), the evidence array contains multiple objects documenting each verification source.

## 7. Refresh & Expiration

### 7.1 Expiration Period

Cornerstone ID credentials expire **3–5 years** after issuance (exact period determined by operational policy).

### 7.2 Refresh Triggers

Credential refresh is required when:
- Expiration date approaches (90 days prior)
- Identity information changes (name change, address update)
- Verification source expires (e.g., BC Person credential expires)
- User requests refresh for any reason

### 7.3 Refresh Process

Refresh requires:
1. Re-verification through a trusted identity source
2. Revocation of previous credential
3. Issuance of new credential with updated `issuanceDate` and `expirationDate`

**Important**: Refresh must NOT introduce role information, property data, or employment data into the credential.

## 8. Revocation Policy

### 8.1 Revocation Triggers

The Cornerstone ID Credential will be revoked when:
1. User account is deleted or closed
2. Fraud or identity mismatch is detected
3. Identity information changes requiring re-issuance (name change)
4. Verification source reports identity change or cancellation
5. Regulatory or legal request for revocation
6. User explicitly requests revocation

### 8.2 Cascade Revocation

Cornerstone ID is the foundation credential referenced by all other credentials via the `cornerstone_id` attribute. While credentials are designed to be independent (no hard technical dependencies at the credential schema level), Cornerstone Network manages cascade revocation operationally:

When a Cornerstone ID is revoked, the platform reviews and revokes:
- **Home Credentials** referencing this Cornerstone ID
- **Professional Credentials** referencing this Cornerstone ID
- **Accreditation Credentials** referencing this Cornerstone ID
- **Portfolio Issuer Credentials** referencing this Cornerstone ID
- **PAACs** issued by or held by this user

### 8.3 Revocation Method

Credential status is managed through the **W3C Bitstring Status List v1.1**:
- **Revocation bitstring**: For permanent revocations (account closure, fraud)
- **Suspension bitstring**: For temporary holds (investigation pending, regulatory review)

Relying parties check credential status before accepting the credential. The platform automatically processes cascade revocations when a Cornerstone ID is revoked.

### 8.4 Post-Revocation

After revocation:
- User may request re-issuance by completing identity verification again
- All dependent credentials must be re-issued separately after Cornerstone ID is re-established

## 9. Schema Versioning

### 9.1 Version Management

The Cornerstone ID schema follows semantic versioning:
- **Major version changes** (v1 → v2): Breaking changes requiring new schema URI
- **Minor version changes** (v1.0 → v1.1): Backward-compatible additions
- **Patch version changes** (v1.0.0 → v1.0.1): Non-breaking fixes

### 9.2 Breaking Changes

Examples of breaking changes requiring new major version:
- Removing required attributes
- Changing attribute data types
- Renaming attributes
- Changing credential structure

## 10. Validation Rules

### 10.1 Required Fields

All of the following fields MUST be present in a valid Cornerstone ID Credential:

**credentialSubject:**
- `given_names`
- `family_name`
- `birthdate`
- `verified_email`
- `verified_phone`
- `cornerstone_user_id`
- `identity_evidence`

**Envelope:**
- `issuer` (must be Cornerstone Network DID)
- `issuanceDate`
- `expirationDate`
- `credentialSchema`
- `credentialStatus`

**Evidence:**
- At least one `evidence` object in the `evidence` array

### 10.2 Forbidden Fields

The following fields MUST NEVER appear in a Cornerstone ID Credential:

- Property/homeownership data (PID, property address, title info, purchase price/date, year built, neighbourhood)
- Employment/professional data (license numbers, employment affiliation, business name)
- Role/persona information (homeowner, professional, advisor indicators)
- Predicates (age predicates, derived booleans)
- Assurance level indicators (proof_level, trust level)
- Financial/mortgage data

## 11. Policy Integration: What This Credential Enables

### 11.1 Capabilities Granted

- **Basic platform access**: Create user account and access platform features
- **Trust network membership**: Eligible to join trust networks (with PAAC)
- **Foundation for all other credentials**: Required before any other credential can be issued
- **Identity verification portability**: Present verified identity to service providers without redundant verification

### 11.2 Related Credentials

**Prerequisites:** None — Cornerstone ID is the foundation credential.

**Referenced by:**
- **Home Credential** — references via `cornerstone_id`
- **Professional Credential** — references via `cornerstone_id`
- **Accreditation Credential** — references via `cornerstone_id`
- **Portfolio Issuer Credential** — references via `cornerstone_id`
- **PAAC** — references homeowner/recipient via platform IDs and DIDs

## 12. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/cornerstone-id-v0.9.json"
  ],
  "type": ["VerifiableCredential", "CornerstoneID"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-01-15T14:32:00Z",
  "validUntil": "2031-01-15T14:32:00Z",
  "credentialSubject": {
    "id": "did:example:user123",
    "given_names": "John Michael",
    "family_name": "Smith",
    "birthdate": "1985-06-21",
    "verified_email": "john.smith@example.com",
    "verified_phone": "+1-604-555-0123",
    "cornerstone_user_id": "550e8400-e29b-41d4-a716-446655440000",
    "postal_address": {
      "street_address": "123 Main St",
      "locality": "Vancouver",
      "region": "BC",
      "postal_code": "V6B 1A1",
      "country": "CA"
    },
    "identity_evidence": "urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890"
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#12345",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "12345",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    },
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/suspension#12345",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "suspension",
      "statusListIndex": "12345",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/suspension"
    }
  ],
  "evidence": [
    {
      "type": "IdentityProofing",
      "method": "InteracBankVerification",
      "verificationDate": "2026-01-15T14:32:00Z",
      "matchFields": ["given_name", "middle_name", "family_name", "birthdate", "phone_number"],
      "recordLocator": "urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "verifier": "Interac Corp."
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/cornerstone-id.json",
    "type": "JsonSchema"
  }
}
```

## 13. References

### 13.1 Industry References

- **vLEI (GLEIF)** — Verifiable LEI ecosystem. The most mature production credential ecosystem, using minimal attribute sets for foundational credentials. Design pattern: foundation credentials should be tight, immutable, and contain only what they uniquely attest to. [gleif.org/vlei](https://www.gleif.org/en/organizational-identity/introducing-the-verifiable-lei-vlei)

- **W3C Verifiable Credentials Data Model** — Standard for JSON-LD verifiable credentials. [w3.org/TR/vc-data-model-2.0](https://www.w3.org/TR/vc-data-model-2.0/)

- **W3C Bitstring Status List v1.1** — Standard mechanism for credential revocation and suspension. [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

- **W3C Data Integrity BBS Cryptosuites** — BBS+ signatures for selective disclosure in JSON-LD credentials. [w3.org/TR/vc-di-bbs](https://www.w3.org/TR/vc-di-bbs/)

### 13.2 Identity Verification Sources

- **Interac Bank Verification Service**: Identity verification through Canadian banking relationships (Canada-wide)
- **BC Person Credential**: Provincial digital identity credential (British Columbia)

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
- **Trust Registry**: `https://trustinfrastructure.com/cornerstone/trust/` *(exact URL TBD)*
