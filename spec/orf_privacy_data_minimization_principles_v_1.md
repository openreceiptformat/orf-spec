# ORF Privacy & Data Minimization Principles

**Version:** 1.0  
**Status:** Draft  
**Applies to:** Open Receipt Format (ORF) v0.1 and later

---

## 1. Introduction

The Open Receipt Format (ORF) is designed to enable interoperable, machine-readable digital receipts while respecting individual privacy, minimizing unnecessary data collection, and supporting global regulatory diversity.

This document defines the **privacy and data minimization principles** that govern ORF implementations. It is a **normative companion** to the ORF Specification and is incorporated by reference.

ORF explicitly recognizes that a valid receipt **does not require identification of a natural person**.

---

## 2. Design Philosophy

ORF privacy design is guided by the following principles:

- **Data minimization by default**
- **Optional identity, not assumed identity**
- **Separation of trust from identity**
- **Jurisdictional compliance without over-collection**
- **Portability and user control**

ORF is intended to function in environments ranging from anonymous cash transactions to highly regulated B2B invoicing systems.

---

## 3. Data Classification

For the purposes of ORF, receipt data is classified into the following categories:

### 3.1 Transactional Data

Information necessary to describe the economic transaction, including:
- Merchant identity
- Line items
- Prices
- Taxes
- Totals

Transactional data is **always permitted**.

### 3.2 Functional Identifiers

Non-personal identifiers used for operational purposes, including:
- Receipt IDs
- Loyalty IDs
- Warranty references
- Visit or session identifiers

Functional identifiers **MUST NOT directly identify a natural person**.

### 3.3 Personal Data

Information that identifies or can reasonably identify a natural person, including:
- Legal names
- Government-issued identifiers
- Tax identification numbers associated with individuals

Personal data is **optional** and **restricted**.

---

## 4. Customer Identity Models

ORF defines three supported customer identity models.

### 4.1 P0 – Anonymous (Default)

No customer object is present.

This model SHOULD be used for:
- Retail purchases
- Cash transactions
- Wallet-based receipts
- Any scenario without a legal identification requirement

### 4.2 P1 – Pseudonymous

A customer object MAY be present containing only functional identifiers.

Examples:
- Loyalty programs
- Returns and warranties
- Appointment or visit references

No personal data is included.

### 4.3 P2 – Identified

Customer-identifying data is present.

This model MUST only be used when:
- Legally required (e.g., VAT invoices, national fiscal systems)
- Explicitly requested or consented to by the customer

---

## 5. Data Minimization Rules

ORF implementations MUST adhere to the following rules:

1. Personal data MUST NOT be included unless required or consented to.
2. Only the **minimum necessary** personal data MAY be included.
3. Optional fields MUST NOT be repurposed to infer identity.
4. Implementers SHOULD prefer functional identifiers over personal data.

Receipts SHOULD remain valid and verifiable in the absence of any customer data.

---

## 6. Consent and Opt-In

When personal data is not legally mandated:

- Inclusion of customer-identifying data MUST be opt-in.
- Consent SHOULD be explicit and revocable where technically feasible.
- Declining identification MUST NOT invalidate the receipt.

ORF does not define consent user interfaces but defines the data expectations that follow consent.

---

## 7. Jurisdictional Overrides

Certain jurisdictions require customer identification for specific transaction types.

In such cases:
- ORF allows identification through jurisdiction-specific extensions.
- Identification requirements MUST be limited to the applicable scope.
- Implementers SHOULD document the legal basis for inclusion.

ORF does not impose additional identification beyond jurisdictional mandates.

---

## 8. Receipt Portability and Wallets

ORF supports portable, user-controlled digital receipts.

Wallet implementations:
- SHOULD support anonymous and pseudonymous receipts
- SHOULD allow users to manage visibility of personal data
- MUST NOT require identification to store or display receipts

Portability MUST NOT be conditioned on identity disclosure.

---

## 9. Security and Integrity Without Identification

ORF separates **receipt trust** from **customer identity**.

- Cryptographic hashes and signatures MAY be used without personal data
- Receipt verification MUST NOT depend on customer identification
- Authenticity SHOULD be provable independently of identity

This enables verifiable receipts for anonymous transactions.

---

## 10. Extensions and Vendor Responsibilities

ORF extensions:

- MUST declare whether they introduce personal data
- MUST justify mandatory personal fields
- MUST follow these privacy principles

Vendor-specific extensions MUST NOT silently introduce identity requirements.

---

## 11. Privacy Conformance Levels

Implementations MAY declare a privacy conformance level:

- **P0 – Anonymous**
- **P1 – Pseudonymous**
- **P2 – Identified**

This declaration supports transparency, auditing, and interoperability.

---

## 12. Change Management

This document may evolve independently of the ORF core schema.

Changes will:
- Be versioned
- Preserve backward compatibility where possible
- Be subject to public review

---

## 13. Out of Scope (Non-Normative)

The following are explicitly out of scope for this document:

- Marketing analytics, behavioral profiling, or advertising use of receipt data
- Cross-merchant or cross-platform customer tracking
- Identity resolution, enrichment, or data brokerage
- Consent management user interfaces
- Legal interpretation or enforcement of privacy regulations

ORF does not standardize downstream data use beyond receipt issuance, storage, and verification.

---

## 14. Non-Normative Examples

### Example A: Anonymous Retail Receipt (P0)

A cash purchase where no customer object is present and the receipt contains only transactional data.

### Example B: Pseudonymous Warranty Receipt (P1)

A receipt containing a customer reference ID used solely for warranty lookup, without any personal identifiers.

### Example C: VAT Invoice with Identified Customer (P2)

A receipt including customer legal name and VAT ID, required to support tax credit claims under EU VAT rules.

These examples are illustrative and do not impose additional requirements.

---

## 15. Relationship to Data Protection Law (Informative)

ORF is designed to be compatible with common data protection frameworks, including GDPR, CCPA/CPRA, and similar regimes.

ORF itself does not determine legal compliance. Implementers are responsible for:
- Determining applicable legal obligations
- Applying appropriate safeguards
- Ensuring lawful basis for processing personal data

The principles in this document support widely accepted privacy concepts such as data minimization, purpose limitation, and user control, without embedding jurisdiction-specific legal requirements.

---

## 16. Summary

ORF is designed to enable interoperable digital receipts **without normalizing surveillance or identity collection**.

Privacy is not an extension of ORF; it is a foundational constraint.

