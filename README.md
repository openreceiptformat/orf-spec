
<img width="261" height="273" alt="ORF-Icon" src="https://github.com/user-attachments/assets/e72ef7ff-0e6d-4acc-850e-aedfde907903" />

# orf-spec
The Open Receipt Format (ORF) is an open, extensible data standard for digital receipts that unifies global receipt practices while preserving local compliance requirements.

# Mission statement:

Our mission is to create a vendor-neutral, extensible specification for digital receipts that supports global tax regimes, industry practices, and future innovation while remaining human-readable and machine-verifiable.

It is **Not** a replacement for national tax standards
**Not** a rendering format (PDF / print / HTML)

It is **A semantic interchange format**

Composable, optional, and jurisdiction-aware

# Core Design Philosophy
## Layered Architecture 
ORF should be explicitly layered:
- Core (Global Minimum)
- Regulatory Extensions
- Industry Extensions
- Vendor / Experimental Extensions

No field outside the core is mandatory.

## ORF Core (Universal, Mandatory)

These are fields that appear in nearly every receipt globally, regardless of tax model.

### Core Entities
**Receipt**

- receipt_id (string, globally unique)
- issue_timestamp (ISO 8601)
- receipt_type (sale, refund, void, proforma)
- currency (ISO 4217)
- total_amount
- payment_status

**Merchant**

- merchant_id
- legal_name
- display_name (optional)
- country_code (ISO 3166-1)

**Transaction**

- transaction_id
- payment_method
- payment_provider (optional)
- LineItem[]
-- line_item_id
-- description
-- quantity
-- unit_price
- line_total

### Optional Standard Modules

Each module is self-contained, versioned, and optional.

**Tax Module (Generic)**

- Supports VAT, GST, Sales Tax, etc.
- tax_scheme (VAT, GST, SALES_TAX)
- tax_lines[]
-- rate
-- base_amount
-- tax_amount
-- jurisdiction

**Jurisdictional Compliance Modules**

These should mirror existing government schemas without redefining them.

Examples:

- mx_cfdi
- br_nfe
- eu_vat
- us_sales_tax
- jp_jct

Each module is

- Namespaced
- Optional
- May include government UUIDs, signatures, hashes

This makes ORF a container, not a competitor.

**Industry-Specific Modules**

Examples:

- Retail
- SKU / GTIN (GS1)
- Discounts
- Loyalty programs
- Hospitality
- Room numbers
- Service periods
- Gratuities
- Healthcare
- Procedure codes
- Provider NPIs
- Claim references

**Payment & Settlement Module**

Aligns with ISO 20022 concepts but stays lightweight.

- authorization_code
- settlement_date
- card_brand
- masked_pan
- wallet_type

### Digital Receipt & Trust Layer

**Verification**

- hash
- hash_algorithm
- signed_by
- signature

**Provenance**

- POS system
- Version of ORF
- Receipt lifecycle events (issued, reissued, voided)

### Rendering Is Explicitly Out of Scope

- ORF defines what a receipt means, not how it looks.

Rendering targets:

- PDF
- HTML
- ESC/POS
- Email
- Wallet passes

These are downstream concerns.

## Governance Model 

- Public specification (GitHub)
- Semantic versioning
- RFC-style proposal process
- Country working groups
- Clear IP policy CC0 1.0 Universal LICENSE

## Competitive Advantage vs “Generic JSON Receipts”

Why ORF is different:

- Modular compliance instead of lowest-common-denominator
- Namespaced regulatory compatibility
- Explicit lifecycle and trust model
- Neutral, open governance
