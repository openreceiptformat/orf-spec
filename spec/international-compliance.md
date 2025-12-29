# ORF International Compliance Mapping

**Version:** 0.1.0  
**Last Updated:** 2024-12-28  
**Status:** Living Document

## Table of Contents

1. [Overview](#overview)
2. [Design Principles](#design-principles)
3. [Jurisdiction Mappings](#jurisdiction-mappings)
   - [Mexico - CFDI](#-mexico---cfdi)
   - [Brazil - NF-e/NFC-e](#-brazil---nf-enfc-e)
   - [European Union - VAT Directive](#-european-union---vat-directive)
   - [United States - Sales Tax](#-united-states---sales-tax)
   - [China - Fapiao](#-china---fapiao)
   - [India - GST e-Invoice](#-india---gst-e-invoice)
   - [Japan - Qualified Invoice](#-japan---qualified-invoice)
   - [Australia - Tax Invoice](#-australia---tax-invoice)
   - [South Korea - E-Tax Invoice](#-south-korea---e-tax-invoice)
4. [Multi-Jurisdiction Example](#multi-jurisdiction-example)
5. [Implementation Guidelines](#implementation-guidelines)
6. [Future Jurisdictions](#future-jurisdictions)

---

## Overview

The Open Receipt Format (ORF) is designed to **coexist with** and **augment** national and regional fiscal receipt standards rather than replace them. ORF provides a canonical, extensible container capable of embedding or referencing jurisdiction-mandated documents while preserving their legal integrity.

### Key Principles

**ORF does not replace fiscal systems.** Instead:
- Fiscal documents live in the `extensions` object
- Legal documents maintain their original format (XML, JSON, etc.)
- ORF provides human-readable access and app interoperability
- Merchants continue to comply with local tax laws

---

## Design Principles

### 1. Non-Invasive Integration
ORF does not modify legally-mandated formats. The original fiscal document (CFDI XML, NF-e XML, etc.) remains the source of truth.

### 2. Container Pattern
Jurisdiction-specific data lives in `extensions.{jurisdiction_code}`, keeping the core ORF schema clean and universal.

### 3. Reference by ID
When full embedding isn't practical, ORF can reference external fiscal documents via URLs or unique identifiers.

### 4. Multi-Jurisdiction Support
A single ORF receipt can reference multiple tax systems simultaneously for international transactions.

### 5. Forward Compatibility
New jurisdictions can be added without breaking existing implementations.

---

## Jurisdiction Mappings

### 🇲🇽 Mexico - CFDI

**Comprobante Fiscal Digital por Internet**

#### Legal Context
All invoices in Mexico must be XML documents stamped by authorized PAC (Proveedor Autorizado de Certificación) providers and submitted to SAT (Servicio de Administración Tributaria).

#### Key Requirements
- **RFC**: Tax identification number (12-13 characters)
- **UUID**: Unique folio fiscal assigned by PAC
- **PAC Stamp**: Digital signature from authorized provider
- **SAT Verification**: Status must be "VIGENTE" (valid)

#### ORF Integration Strategy
Embed CFDI UUID and verification metadata in `extensions.mx_cfdi`. The CFDI XML remains the legal document and must be available via URL.

#### Required Fields
- `version`: CFDI schema version (3.3 or 4.0)
- `uuid`: Folio fiscal (UUID format)
- `pac_provider`: Authorized PAC provider name
- `sat_status`: VIGENTE, CANCELADO, or NO_ENCONTRADO

#### Compliance Notes
- CFDI XML is the legally binding document
- ORF receipt provides user-friendly view + quick access
- QR code enables instant SAT verification
- Cancellations must be processed through SAT system

#### Use Cases
- Retail sales (B2C and B2B)
- Professional services
- Product returns (requires cancellation or "complemento")

---

### 🇧🇷 Brazil - NF-e/NFC-e

**Nota Fiscal Eletrônica / Nota Fiscal de Consumidor Eletrônica**

#### Legal Context
Electronic invoices must be authorized by SEFAZ (Secretaria da Fazenda) state tax authority and contain a 44-digit access key (chave de acesso).

#### Key Requirements
- **CNPJ**: Corporate tax ID (14 digits)
- **CPF**: Individual tax ID (11 digits) for B2C
- **Access Key**: 44-digit unique identifier
- **Authorization Protocol**: 15-digit SEFAZ approval number
- **DANFE**: Human-readable PDF auxiliary document

#### ORF Integration Strategy
Embed NF-e access key and authorization metadata in `extensions.br_nfe`. The NF-e XML is the legal document.

#### Required Fields
- `model`: 55 (NF-e) or 65 (NFC-e)
- `access_key`: 44-digit chave de acesso
- `authorization_protocol`: 15-digit SEFAZ number

#### Compliance Notes
- NF-e (model 55): B2B transactions, detailed item info
- NFC-e (model 65): B2C retail, simplified format
- QR code mandatory for NFC-e (consumer verification)
- XML must be stored for 5+ years
- DANFE PDF used for shipping/visual verification

#### Use Cases
- Product sales (goods and merchandise)
- Returns and exchanges (must reference original NF-e)
- Inter-state transfers

---

### 🇪🇺 European Union - VAT Directive

**Value Added Tax Compliance (Directive 2006/112/EC)**

#### Legal Context
VAT-registered businesses must issue invoices containing specific fields including VAT identification numbers and tax breakdowns by rate.

#### Key Requirements
- **VAT Number**: Format varies by member state (e.g., DE123456789)
- **Tax Breakdown**: Separate totals for each VAT rate
- **Reverse Charge**: Indicator for B2B cross-border supplies
- **Intra-Community Supply**: Special treatment for EU B2B

#### ORF Integration Strategy
Embed VAT-specific requirements in `extensions.eu_vat`. Member state determines additional requirements.

#### Required Fields
- `invoice_type`: COMMERCIAL_INVOICE, CREDIT_NOTE, DEBIT_NOTE, or SIMPLIFIED_INVOICE
- `member_state`: ISO 3166-1 alpha-2 country code

#### Compliance Notes
- Standard rates vary by country (17-27%)
- Reduced rates for food, books, medicine
- Zero-rated for exports outside EU
- Reverse charge for intra-EU B2B (buyer pays VAT)
- Digital services use "Mini One-Stop Shop" (MOSS)

#### Use Cases
- Domestic B2B and B2C sales
- Intra-EU supplies (reverse charge)
- Exports (zero-rated with proof of export)
- Digital services across borders

---

### 🇺🇸 United States - Sales Tax

**State and Local Sales Tax**

#### Legal Context
Sales tax varies by state and locality. No federal sales tax. Requirements differ by state; some require nexus determination.

#### Key Requirements
- **EIN**: Employer Identification Number (federal)
- **State Tax ID**: State-specific registration number
- **Nexus**: Physical or economic presence in state
- **Jurisdiction Codes**: For multi-tier taxation (state + county + city)

#### ORF Integration Strategy
Embed state-specific tax information in `extensions.us_sales_tax`. Support multi-tier tax breakdown.

#### Required Fields
- `state`: 2-letter state code
- `nexus`: Boolean indicating presence in state

#### Compliance Notes
- Five states have no sales tax (AK, DE, MT, NH, OR)
- Origin-based vs. destination-based sourcing
- Marketplace facilitator laws (Amazon, eBay collect tax)
- Economic nexus thresholds (typically $100k revenue or 200 transactions)
- Streamlined Sales Tax Agreement (24 member states)

#### Use Cases
- Retail sales (in-person and online)
- Digital goods and services
- Tax-exempt purchases (resale certificates, non-profits)
- Multi-state sellers (nexus determination)

---

### 🇨🇳 China - Fapiao

**发票 (Invoice) - Golden Tax System**

#### Legal Context
All invoices must be issued through the Golden Tax System (增值税发票管理新系统) and contain unique Fapiao code and number. VAT Fapiaos require tax authority verification.

#### Key Requirements
- **USCC**: Unified Social Credit Code (18 characters)
- **Tax Number**: Tax registration number
- **Fapiao Code**: 10-digit invoice code
- **Fapiao Number**: 8-digit sequential number
- **Verification Code**: 20-digit code for validation
- **Machine Code**: Tax control device identifier

#### ORF Integration Strategy
Embed Fapiao verification data in `extensions.cn_fapiao`. Different types for different use cases.

#### Required Fields
- `fapiao_type`: VAT_SPECIAL, VAT_ORDINARY, E_FAPIAO, or QUOTA_INVOICE
- `fapiao_code`: 10-digit code
- `fapiao_number`: 8-digit number
- `verification_code`: 20-digit code

#### Compliance Notes
- VAT Special Fapiao: Allows input tax deduction (B2B)
- VAT Ordinary Fapiao: No deduction allowed (B2C)
- E-Fapiao: Fully electronic, no paper
- Red Invoice: Negative amount for returns/corrections
- QR code verification via State Tax Administration app
- Three-role system: drawer, reviewer, payee

#### Use Cases
- B2B sales requiring VAT deduction
- Retail consumer sales
- Professional services
- Returns and refunds (red invoice)

---

### 🇮🇳 India - GST e-Invoice

**Goods and Services Tax Electronic Invoice**

#### Legal Context
B2B invoices above certain thresholds must be registered on the Invoice Registration Portal (IRP) and receive an Invoice Reference Number (IRN) with QR code.

#### Key Requirements
- **GSTIN**: 15-character GST Identification Number
- **PAN**: Permanent Account Number (10 characters)
- **IRN**: 64-character hexadecimal unique identifier
- **Acknowledgement Number**: 15-digit IRP confirmation
- **HSN Code**: Harmonized System of Nomenclature code

#### ORF Integration Strategy
Embed GST e-Invoice data in `extensions.in_gst_einvoice`. IRN enables real-time verification.

#### Required Fields
- `version`: e-Invoice schema version (1.0 or 1.1)
- `irn`: 64-character hash
- `acknowledgement_number`: 15-digit IRP number

#### Compliance Notes
- Mandatory for businesses with turnover > ₹5 crores
- B2B, B2C, export, and SEZ supplies supported
- IRN generated by GSTN via IRP
- QR code contains digitally signed invoice summary
- Integrates with e-Way Bill system for transportation
- CGST + SGST for intra-state, IGST for inter-state

#### Use Cases
- B2B supplies above threshold
- Export invoices
- SEZ supplies (with/without payment)
- Credit and debit notes

---

### 🇯🇵 Japan - Qualified Invoice

**適格請求書 (Tekikaku Seikyūsho)**

#### Legal Context
From October 2023, Qualified Invoice Issuer Registration Number (登録番号) required for input tax credit under consumption tax system.

#### Key Requirements
- **Registration Number**: T + 13 digits (corporate number)
- **Two Tax Rates**: 10% standard, 8% reduced
- **Clear Breakdown**: Separate totals for each rate
- **Simplified Format**: Allowed for retail/restaurants

#### ORF Integration Strategy
Embed Qualified Invoice data in `extensions.jp_qualified_invoice`. Support both full and simplified formats.

#### Required Fields
- `invoice_type`: QUALIFIED_INVOICE or SIMPLIFIED_INVOICE
- `registration_number`: T + 13 digits
- `tax_rate_type`: STANDARD_10, REDUCED_8, or EXEMPT

#### Compliance Notes
- Registration required to issue qualified invoices
- 10% standard rate (most goods/services)
- 8% reduced rate (food, beverages, newspapers)
- Simplified invoices: retail/restaurant, no buyer name needed
- Must clearly identify reduced-rate items
- Six-year retention requirement

#### Use Cases
- B2B transactions (full qualified invoice)
- Retail/restaurant sales (simplified invoice)
- Mixed-rate transactions (food + other goods)
- Tax-exempt transactions (exports, certain services)

---

### 🇦🇺 Australia - Tax Invoice

**Goods and Services Tax (GST) Tax Invoice**

#### Legal Context
GST-registered businesses must issue tax invoices for sales over A$82.50 (including GST). Must include ABN and "Tax Invoice" notation for amounts over A$1,000.

#### Key Requirements
- **ABN**: 11-digit Australian Business Number
- **ACN**: Australian Company Number (optional)
- **GST Amount**: Must be clearly stated
- **"Tax Invoice" Words**: Required for invoices over A$1,000

#### ORF Integration Strategy
Embed Australian tax invoice requirements in `extensions.au_tax_invoice`. Support GST-free and input-taxed supplies.

#### Required Fields
- `document_type`: TAX_INVOICE, RCTI, or ADJUSTMENT_NOTE
- `abn`: 11 digits formatted with spaces
- `gst_amount`: Total GST (10% of taxable supplies)

#### Compliance Notes
- 10% GST on most goods and services
- GST-free: exports, basic food, medical, education
- Input-taxed: financial services, residential rent
- RCTI (Recipient Created Tax Invoice): by agreement only
- Tax invoices required for purchases > A$82.50
- Adjustment notes for corrections

#### Use Cases
- Standard B2B and B2C sales
- GST-free supplies (exports, basic food)
- Recipient created tax invoices (RCTI)
- Adjustment notes (price changes, returns)

---

### 🇰🇷 South Korea - E-Tax Invoice

**전자세금계산서 (Electronic Tax Invoice)**

#### Legal Context
VAT-registered businesses must issue electronic tax invoices through National Tax Service (국세청) approved systems.

#### Key Requirements
- **Business Registration Number**: 10 digits (###-##-#####)
- **NTS Approval Number**: Issued after electronic transmission
- **Receipt Confirmation**: Buyer must confirm within 24 hours
- **Business Type/Item**: Industry classification required

#### ORF Integration Strategy
Embed Korean e-tax invoice data in `extensions.kr_etax_invoice`. Track confirmation status.

#### Required Fields
- `invoice_type`: GENERAL, MODIFIED, or FINAL
- `approval_number`: Format YYYYMMDD-############-########
- `supplier_registration_number`: ###-##-#####
- `buyer_registration_number`: ###-##-#####

#### Compliance Notes
- 10% VAT rate (standard, no reduced rates)
- Must transmit through NTS-approved ASP or directly to NTS
- Buyer confirmation required within 24 hours
- Late issuance penalties (must issue by 10th of following month)
- Modified invoices reference original invoice
- Both buyer and seller details required

#### Use Cases
- All B2B transactions
- Modified invoices (corrections)
- Final invoices (end of contract/project)
- Credit and debit adjustments

---

## Multi-Jurisdiction Example

**Scenario**: A global merchant operating in multiple countries issues a single receipt that complies with various tax systems simultaneously.

```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "ORF-GLOBAL-2024-001",
    "issue_timestamp": "2024-12-28T12:00:00Z",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "Global eCommerce Ltd.",
    "country_code": "GB",
    "tax_identifiers": [
      { "scheme": "EU_VAT", "value": "GB123456789" },
      { "scheme": "US_EIN", "value": "98-7654321" },
      { "scheme": "BR_CNPJ", "value": "12.345.678/0001-90" }
    ]
  },
  "customer": {
    "legal_name": "International Corp.",
    "tax_identifiers": [
      { "scheme": "EU_VAT", "value": "DE987654321" }
    ]
  },
  "line_items": [
    {
      "description": "Cloud Services - Annual Subscription",
      "quantity": 1,
      "unit_price": 10000.00,
      "line_total": 10000.00
    }
  ],
  "tax": {
    "tax_scheme": "MULTI",
    "tax_lines": [
      {
        "rate": 0.20,
        "base_amount": 10000.00,
        "tax_amount": 2000.00,
        "jurisdiction": "GB-VAT"
      }
    ]
  },
  "total": 12000.00,
  "currency": "USD",
  "extensions": {
    "eu_vat": {
      "member_state": "GB",
      "reverse_charge": true,
      "invoice_type": "COMMERCIAL_INVOICE",
      "intra_community_supply": false
    },
    "us_sales_tax": {
      "state": "CA",
      "nexus": false,
      "note": "Digital service - not subject to CA sales tax per current regulations"
    },
    "br_nfe": {
      "note": "Service delivered outside Brazil - NF-e not applicable"
    }
  }
}
```

---

## Implementation Guidelines

### For POS Vendors

#### 1. Generate Both Formats
Create jurisdiction-required document (CFDI XML, NF-e XML, etc.) AND ORF JSON in parallel.

#### 2. Embed References
Include links/IDs to official documents in `extensions`. Never replace legal formats.

#### 3. Support Multi-Jurisdiction
Allow multiple `extensions` objects to coexist for international businesses.

#### 4. Provide Validation
Validate against both ORF core schema and jurisdiction-specific rules before issuing.

### For App Developers

#### 1. Parse Core Fields First
Always read standard ORF fields (merchant, items, total) before checking extensions.

#### 2. Check Extensions
Look for jurisdiction-specific data in `extensions` based on merchant country or tax identifiers.

#### 3. Graceful Degradation
Handle missing `extensions` gracefully - display core ORF data even without jurisdiction details.

#### 4. Respect Legal Requirements
When displaying receipts, provide links to official fiscal documents for audit/verification.

### For Merchants

#### 1. Compliance First
Always generate legally-required documents per local regulations.

#### 2. ORF as Bonus
Offer ORF receipts as user-friendly supplement, not replacement.

#### 3. Link Both
Provide easy access to both ORF (for apps/users) and official documents (for audits/authorities).

#### 4. Retain Legal Documents
Store fiscal documents (XML, PDF) for legally-mandated retention periods.

---

## Future Jurisdictions

This document will be expanded to include:

- 🇨🇦 Canada - GST/HST
- 🇸🇬 Singapore - GST e-Invoice
- 🇹🇭 Thailand - e-Tax Invoice
- 🇲🇾 Malaysia - e-Invoice
- 🇿🇦 South Africa - Tax Invoice
- 🇦🇪 UAE - VAT Tax Invoice
- 🇸🇦 Saudi Arabia - ZATCA e-Invoice

### Contributing New Jurisdictions

Community contributions are welcome! To add a new jurisdiction:

1. **Open an issue** describing the jurisdiction and legal requirements
2. **Provide documentation** links to official tax authority specifications
3. **Submit example** ORF receipt with `extensions.{jurisdiction_code}`
4. **Define validation rules** (required fields, patterns, enums)

See [CONTRIBUTING.md](../CONTRIBUTING.md) for full guidelines.

---

## Legal Disclaimer

ORF is a technical standard and does not provide legal or tax advice. Merchants must ensure compliance with local tax and invoicing laws. This document provides technical guidance for integration but does not constitute legal, tax, or accounting advice.

**Always consult with local tax authorities or legal counsel for compliance requirements specific to your jurisdiction and business.**

---

## Version History

- **0.1.0** (2024-12-28): Initial release with 9 jurisdictions
  - Mexico (CFDI)
  - Brazil (NF-e/NFC-e)
  - European Union (VAT)
  - United States (Sales Tax)
  - China (Fapiao)
  - India (GST e-Invoice)
  - Japan (Qualified Invoice)
  - Australia (Tax Invoice)
  - South Korea (E-Tax Invoice)

---

**Questions or feedback?** Open a [discussion](https://github.com/openreceiptformat/orf-spec/discussions) or [issue](https://github.com/openreceiptformat/orf-spec/issues) on GitHub.
