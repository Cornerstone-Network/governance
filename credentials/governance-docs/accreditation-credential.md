# Accreditation Credential – Credential Governance

## 1. About this Document

This document describes the **Accreditation Credential** verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes entities that need to confirm the licensing status of professionals.

The Accreditation Credential is issued by the **Cornerstone Network** and represents authoritative proof of a professional's verified licensing or registration information obtained from provincial regulators such as:

- [**RECO** (Real Estate Council of Ontario)](https://registrantsearch.reco.on.ca/)
- [**BCFSA** (BC Financial Services Authority)](https://www.bcfsa.ca/)

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **1.0**   | 26-Feb-2026 | JSON-LD format finalization; removed `licence_status` and `discipline_notes` (status managed via credential status mechanism); updated schema hosting; added design rationale with industry research references | Mathieu Glaude |
| **0.1**   | 4-Dec-2025  | Initial draft (split from Professional Credential) | Mathieu Glaude |

## 2. Credential Overview

The Accreditation Credential is a verifiable credential (VC) issued to individuals who are confirmed as licensed or registered professionals by their provincial regulator. This credential focuses exclusively on attesting to the **fact of licensure** and does not include employment, identity, or mutable regulatory status information.

The credential references the holder's **Cornerstone ID** for identity binding, rather than duplicating identity claims.

Each issuance is backed by:
- Regulator registry verification (name + licence match)
- Reference to holder's verified Cornerstone ID

The credential is issued directly into the **Cornerstone Wallet** and can be consumed by relying parties to verify professional licensing.

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Accreditation Credential                                    |
| **Schema:**     | Accreditation Credential v1.0                               |
| **Issuer:**     | Cornerstone Network                                         |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`)   |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                        |

### 2.1 Attribute Summary (8 Attributes)

| **#** | **Name**            | **Attribute**          | **Data Type**       | **Required** | **Notes** |
|-------|---------------------|------------------------|---------------------|--------------|-----------|
| 001   | Cornerstone ID Reference | `cornerstone_id` | DID/URI             | Yes          | Reference to holder's Cornerstone ID. |
| 002   | Profession Category | `profession_category`  | String              | Yes          | Free-form for v1 (e.g., "Real Estate Broker", "Appraiser"). |
| 003   | Licence Number      | `licence_number`       | String              | Yes          | Regulatory licence/registration number. |
| 004   | Licence Expiry      | `licence_expiry`       | String (YYYY-MM-DD) | Yes          | Expiry date from regulator record. |
| 005   | Regulator Name      | `regulator_name`       | String              | Yes          | e.g., "RECO", "BCFSA". |
| 006   | Regulator URI       | `regulator_uri`        | URI                 | Yes          | Link to regulator's public registry. |
| 007   | Jurisdiction        | `jurisdiction`         | String              | Yes          | Province of registration (e.g., ON, BC). |
| 008   | Evidence Reference  | `evidence`             | URI                 | Yes          | Reference to verification evidence. |

### 2.2 Design Rationale

This credential underwent the most significant design changes in this version, informed by extensive research into how production credential ecosystems handle professional licensing and regulatory status.

**Attribute decisions in this version:**

- **Removed `licence_status`** (REGISTERED/LICENSED/SUSPENDED). Embedding mutable status in a signed credential is an anti-pattern identified across every production credential ecosystem studied. A verifiable credential is a cryptographically signed, point-in-time attestation. The credential attests to the *fact of licensure* at issuance time. Status changes are handled through the **W3C Bitstring Status List v1.1**, which supports both `revocation` (permanent — licence permanently cancelled) and `suspension` (reversible — licence temporarily suspended pending investigation or remediation). When a licence is suspended by the regulator, the credential's suspension bit is flipped — no reissuance needed. When the suspension is lifted, the bit is cleared. This eliminates the staleness risk where a credential shows "ACTIVE" when the person has since been suspended.

- **Removed `discipline_notes`**. Disciplinary records are mutable, narrative, and sensitive. Baking them into a signed credential creates three problems: (1) **Immutability conflict** — the credential would need to be reissued every time discipline status changes; (2) **Staleness risk** — a credential showing "no discipline" could be stale when conditions were added; (3) **Privacy risk** — disciplinary notes are sensitive data that exists in the signed blob even with selective disclosure. Verifiers who need detailed disciplinary information can check the regulator's public registry, which is linked via `regulator_uri`. This separation of concerns follows the pattern used by every production credential ecosystem studied.

- **`profession_category` is free-form for v1.** To be standardized with a controlled vocabulary in a future version. Current examples: "Real Estate Broker", "Real Estate Representative", "Appraiser", "Mortgage Broker".

- **Cornerstone Network verifies against regulators manually.** Cornerstone verifies against regulator registries (RECO, BCFSA) before issuance. There is a process to get the information and provide it to the network so that credential offers pick up the right information. The verification process and evidence are captured via the `evidence` reference for FINTRAC compliance and audit trail.

- **This credential is independent.** No hard dependency on Professional or Portfolio Issuer credentials. A person may hold only a Professional Credential (employed but not licensed), only an Accreditation Credential (licensed but not employed), or both.

**The recommended pattern for handling regulatory status:**

```
+----------------------------+       +---------------------------+
| Accreditation Credential   |       | Credential Status Service |
| (Immutable attestation)    |       | (Mutable, external)       |
|                            |       |                           |
| - Subject identity ref     |  refs | - Revocation bitstring    |
| - Licence number           | ----> | - Suspension bitstring    |
| - Licence type             |       | - Updated per policy      |
| - Issuing authority        |       |                           |
| - Issuance date            |       +---------------------------+
| - Validity period          |
| - credentialStatus (URL)   |       +---------------------------+
|                            |       | Regulator Public Registry |
+----------------------------+       | (Complementary, external) |
                                     |                           |
                                     | - Disciplinary actions    |
                                     | - Conditions on licence   |
                                     | - Historical status       |
                                     | - regulator_uri links here|
                                     +---------------------------+
```

### 2.3 Removed Attributes (v1.0)

| Attribute | Reason for Removal |
|-----------|-------------------|
| `licence_status` (String: REGISTERED/LICENSED/SUSPENDED/REVOKED) | Mutable status in a signed credential is an anti-pattern. Status changes handled via W3C Bitstring Status List (revocation + suspension bitstrings). Every production VC ecosystem studied (vLEI, OCI, BC OrgBook) follows this pattern. |
| `discipline_notes` (String) | Mutable, narrative, sensitive data. Creates immutability conflict, staleness risk, and privacy concerns. Verifiers should check regulator's public registry via `regulator_uri` for disciplinary details. |

## 3. Credential Details

### 3.1 Issuer

The Accreditation Credential is issued by the **Cornerstone Network**, acting as the credential authority. Each credential is issued only after regulator registry checks are successfully completed and matched to a verified Cornerstone ID.

### 3.2 Issuer Data Source

The data comes from authoritative provincial regulator registries:

- **RECO** (Real Estate Council of Ontario) — licence category, number, expiry
- **BCFSA** (BC Financial Services Authority) — registration category, number, expiry
- Other provincial regulators as supported

Cornerstone Network verifies against these registries manually before issuance.

#### 3.2.1 Data Updates
- A credential reflects the state of regulator records at issuance.
- Licence status changes are handled via credential status mechanism (suspension/revocation bits).
- Licence renewal or upgrade requires revocation of old credential and issuance of new one.
- Evidence is retained for five years in compliance with FINTRAC.

### 3.3 Assurance

The credential combines two assurance anchors:
- Verified Cornerstone ID (identity already established)
- Regulator licence/registry record match

### 3.4 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**:

| Regulatory Event | Credential Action | Status List Action |
|---|---|---|
| Licence granted | Issue new credential | Index set to 0 (valid) |
| Licence suspended | No new credential | Set suspension bit to 1 |
| Suspension lifted | No new credential | Set suspension bit back to 0 |
| Licence revoked (permanent) | No new credential | Set revocation bit to 1 |
| Licence renewed/upgraded | Revoke old, issue new | Old: revocation=1; New: index=0 |

### 3.5 Revocation

An Accreditation Credential will be revoked in cases such as:
1. Licence permanently revoked by regulator
2. Licence expired and not renewed
3. Cornerstone ID revoked or invalidated
4. Fraud or errors detected
5. Holder requests re-issuance

Re-issuance involves re-verification against regulator registry and issuance of a new credential.

## 4. Credential Definition

### 4.1 Credential Schema

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/accreditation-credential.json` *(exact URL TBD)*
- **Schema Versioning:** Semantic versioning. Breaking changes produce a new major version.
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/accreditation-credential-v1.json` *(exact URL TBD)*

### 4.2 Subject of the Credential

The subject is the **individual holder**, bound to a **professional licence** at issuance. Binding is confirmed by reference to verified Cornerstone ID and regulator record match.

### 4.3 Attributes

#### 4.3.1 Identity Reference

*Cornerstone ID Reference (001)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_id</code></td></tr>
  <tr><th>Description</th><td>Reference to the holder's verified Cornerstone ID credential.</td></tr>
  <tr><th>Source</th><td>Cornerstone ID issuance.</td></tr>
  <tr><th>Data Type</th><td>DID/URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.2 Regulatory Attributes

*Profession Category (002)*

<table>
  <tr><th>Attribute</th><td><code>profession_category</code></td></tr>
  <tr><th>Description</th><td>Professional category. Free-form for v1; to be standardized with controlled vocabulary in future version.</td></tr>
  <tr><th>Source</th><td>Provincial regulator registry.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Real Estate Broker</code>, <code>Real Estate Representative</code>, <code>Appraiser</code>, <code>Mortgage Broker</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Licence Number (003)*

<table>
  <tr><th>Attribute</th><td><code>licence_number</code></td></tr>
  <tr><th>Description</th><td>Unique licence/registration number issued by regulator.</td></tr>
  <tr><th>Source</th><td>Provincial regulator registry (e.g., RECO, BCFSA).</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Licence Expiry (004)*

<table>
  <tr><th>Attribute</th><td><code>licence_expiry</code></td></tr>
  <tr><th>Description</th><td>Expiry date of licence/registration.</td></tr>
  <tr><th>Source</th><td>Provincial regulator registry.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Regulator Name (005)*

<table>
  <tr><th>Attribute</th><td><code>regulator_name</code></td></tr>
  <tr><th>Description</th><td>Name of the regulatory body that issued the licence.</td></tr>
  <tr><th>Source</th><td>System configuration.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>RECO</code>, <code>BCFSA</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Regulator URI (006)*

<table>
  <tr><th>Attribute</th><td><code>regulator_uri</code></td></tr>
  <tr><th>Description</th><td>URL to the regulator's public registry or website. Verifiers can check this URI for current disciplinary status, conditions, and detailed regulatory standing.</td></tr>
  <tr><th>Source</th><td>System configuration.</td></tr>
  <tr><th>Data Type</th><td>URI</td></tr>
  <tr><th>Examples</th><td><code>https://registrantsearch.reco.on.ca/</code>, <code>https://www.bcfsa.ca/</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Design Note</th><td>This URI serves as the link to detailed regulatory information (including discipline/conditions) that is intentionally not embedded in the credential. See Design Rationale section 2.2.</td></tr>
</table>

*Jurisdiction (007)*

<table>
  <tr><th>Attribute</th><td><code>jurisdiction</code></td></tr>
  <tr><th>Description</th><td>Province or territorial jurisdiction under which the professional is licensed.</td></tr>
  <tr><th>Source</th><td>Provincial regulator registry.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>ON</code>, <code>BC</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.3 Evidence Attributes

*Evidence Reference (008)*

<table>
  <tr><th>Attribute</th><td><code>evidence</code></td></tr>
  <tr><th>Description</th><td>URI referencing the regulator verification record and verification date.</td></tr>
  <tr><th>Source</th><td>Cornerstone verification process.</td></tr>
  <tr><th>Data Type</th><td>URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

## 5. Validation Rules

### 5.1 Required Fields

**credentialSubject:**
- `cornerstone_id`
- `profession_category`
- `licence_number`
- `licence_expiry`
- `regulator_name`
- `regulator_uri`
- `jurisdiction`
- `evidence`

**Envelope:**
- `issuer` (must be Cornerstone Network DID)
- `validFrom`
- `validUntil`
- `credentialSchema`
- `credentialStatus` (must include both revocation and suspension entries)

## 6. Policy Integration

### 6.1 Related Credentials

- **References**: Cornerstone ID (via `cornerstone_id`)
- **Independent of**: Professional Credential, Portfolio Issuer Credential
- A professional may hold: only Professional Credential (employed, not licensed), only Accreditation Credential (licensed, not employed), or both

### 6.2 Industry Context

No production verifiable credential implementations exist for real estate professional licensing. ARELLO (Association of Real Estate License Law Officials) maintains a centralized database of 4.3M+ licensee records, but it is a traditional database system. This credential represents a **greenfield implementation** and an opportunity to set the pattern for the industry.

## 7. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/accreditation-credential-v1.json"
  ],
  "type": ["VerifiableCredential", "AccreditationCredential"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-03-01T10:00:00Z",
  "validUntil": "2028-03-01T10:00:00Z",
  "credentialSubject": {
    "id": "did:example:professional789",
    "cornerstone_id": "did:example:professional789",
    "profession_category": "Real Estate Broker",
    "licence_number": "RE-2025-004821",
    "licence_expiry": "2028-03-01",
    "regulator_name": "RECO",
    "regulator_uri": "https://registrantsearch.reco.on.ca/",
    "jurisdiction": "ON",
    "evidence": "urn:uuid:d4e5f6a7-b8c9-0123-def0-123456789012"
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#45678",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "45678",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    },
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/suspension#45678",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "suspension",
      "statusListIndex": "45678",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/suspension"
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/accreditation-credential.json",
    "type": "JsonSchema"
  }
}
```

## 8. References

### 8.1 Industry References

- **OCI (Open Credentialing Initiative)** — Pharmaceutical ATP credential attests to the fact of licensure without embedding status or disciplinary data. Status managed via RevocationStatus2021. [open-credentialing-initiative.github.io](https://open-credentialing-initiative.github.io/schemas/specification/latest/index.html)

- **vLEI (GLEIF)** — No mutable status in any credential. Status changes handled entirely through external status registries. [gleif.org/vlei](https://www.gleif.org/en/organizational-identity/introducing-the-verifiable-lei-vlei)

- **BC OrgBook** — 4+ million verifiable credentials about organizations. Status changes trigger new credential issuance rather than embedding status as an attribute. [orgbook.gov.bc.ca](https://orgbook.gov.bc.ca/)

- **W3C Bitstring Status List v1.1** — Standard mechanism for credential revocation and suspension with separate bitstrings. Supports reversible suspension, ideal for regulatory licence holds. [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

- **W3C Data Integrity BBS Cryptosuites** — BBS+ signatures for selective disclosure. Even with selective disclosure available, mutable data like discipline notes should not be embedded — BBS+ solves the privacy problem but not the staleness problem. [w3.org/TR/vc-di-bbs](https://www.w3.org/TR/vc-di-bbs/)

- **Real estate industry note** — No production VC implementations exist for real estate professional licensing. ARELLO (Association of Real Estate License Law Officials) maintains a centralized database (traditional system). This credential is a greenfield implementation and an opportunity to set the industry pattern.

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
