# ORF International Compliance Mapping

**Version:** 0.1.0  
**Last Updated:** 2024-12-28

## Overview

The Open Receipt Format (ORF) is designed to **coexist with** and **augment** national and regional fiscal receipt standards rather than replace them. ORF provides a canonical, extensible container capable of embedding or referencing jurisdiction-mandated documents while preserving their legal integrity.

## Design Principles

1. **Non-Invasive**: ORF does not modify legally-mandated formats
2. **Container Pattern**: Fiscal documents live in `extensions` object
3. **Reference by ID**: Link to external fiscal documents when full embedding isn't practical
4. **Multi-Jurisdiction**: Single receipt can reference multiple tax systems
5. **Forward Compatible**: New jurisdictions can be added without breaking changes

---

## Jurisdiction Mapping

### 🇲🇽 Mexico - CFDI (Comprobante Fiscal Digital por Internet)

**Legal Requirement**: All invoices must be XML documents stamped by authorized PAC providers and submitted to SAT (tax authority).

**ORF Integration Strategy**: Embed CFDI UUID and verification metadata in `extensions.mx_cfdi`

**Example**:
```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "ORF-MX-2024-001",
    "issue_timestamp": "2024-12-28T10:30:00-06:00",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "Tienda Demo S.A. de C.V.",
    "country_code": "MX",
    "tax_identifiers": [
      {
        "scheme": "RFC",
        "value": "TDE920101AA1"
      }
    ]
  },
  "customer": {
    "legal_name": "Juan Pérez García",
    "tax_identifiers": [
      {
        "scheme": "RFC",
        "value": "PEGJ850101XY9"
      }
    ]
  },
  "line_items": [
    {
      "description": "Laptop Dell Inspiron 15",
      "quantity": 1,
      "unit_price": 12000.00,
      "line_total": 12000.00
    }
  ],
  "tax": {
    "tax_scheme": "MX_IVA",
    "tax_lines": [
      {
        "rate": 0.16,
        "base_amount": 12000.00,
        "tax_amount": 1920.00,
        "jurisdiction": "MX-FEDERAL"
      }
    ]
  },
  "total": 13920.00,
  "currency": "MXN",
  "extensions": {
    "mx_cfdi": {
      "version": "4.0",
      "uuid": "550e8400-e29b-41d4-a716-446655440000",
      "folio_fiscal": "550e8400-e29b-41d4-a716-446655440000",
      "pac_provider": "PAC-DEMO-SA",
      "pac_stamp_timestamp": "2024-12-28T10:30:15-06:00",
      "certificate_number": "20001000000300022815",
      "sat_certificate": "00001000000407657428",
      "original_string": "||4.0|...|...",
      "sat_stamp": "MEQCIH...",
      "qr_code_url": "https://verificacfdi.facturaelectronica.sat.gob.mx/...",
      "xml_url": "https://example.com/cfdi/550e8400.xml",
      "sat_status": "VIGENTE"
    }
  }
}
```

**Key Fields**:
- `uuid`: Unique identifier assigned by PAC (legally required)
- `pac_provider`: Authorized stamping provider
- `sat_status`: Verification status from SAT
- `xml_url`: Link to full CFDI XML (legally required format)

**Compliance Notes**:
- CFDI XML remains the legal document
- ORF receipt provides human-readable view + quick access
- QR code enables instant SAT verification

---

### 🇧🇷 Brazil - NF-e / NFC-e (Nota Fiscal Eletrônica)

**Legal Requirement**: Electronic invoices must be authorized by SEFAZ (state tax authority) and contain a 44-digit access key.

**ORF Integration Strategy**: Embed NF-e access key and authorization metadata in `extensions.br_nfe`

**Example**:
```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "ORF-BR-2024-001",
    "issue_timestamp": "2024-12-28T14:45:00-03:00",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "Loja Exemplo LTDA",
    "country_code": "BR",
    "tax_identifiers": [
      {
        "scheme": "CNPJ",
        "value": "12.345.678/0001-90"
      },
      {
        "scheme": "IE",
        "value": "123.456.789.012"
      }
    ]
  },
  "customer": {
    "legal_name": "Maria Silva Santos",
    "tax_identifiers": [
      {
        "scheme": "CPF",
        "value": "123.456.789-00"
      }
    ]
  },
  "line_items": [
    {
      "description": "Smartphone Samsung Galaxy A54",
      "quantity": 1,
      "unit_price": 1500.00,
      "line_total": 1500.00
    }
  ],
  "tax": {
    "tax_scheme": "BR_ICMS_IPI",
    "tax_lines": [
      {
        "rate": 0.18,
        "base_amount": 1500.00,
        "tax_amount": 270.00,
        "jurisdiction": "BR-SP-ICMS"
      }
    ]
  },
  "total": 1500.00,
  "currency": "BRL",
  "extensions": {
    "br_nfe": {
      "model": "65",
      "series": "1",
      "number": "123456",
      "access_key": "35240812345678000190650010001234561000123456",
      "authorization_protocol": "135240000000123",
      "authorization_timestamp": "2024-12-28T14:45:30-03:00",
      "digest_value": "a1b2c3d4e5f6...",
      "qr_code_url": "https://www.sefaz.sp.gov.br/nfce/qrcode?p=35240812...",
      "qr_code_data": "35240812345678000190650010001234561000123456|2|1|1500.00|...",
      "xml_url": "https://example.com/nfe/35240812345678.xml",
      "danfe_url": "https://example.com/danfe/35240812345678.pdf"
    }
  }
}
```

**Key Fields**:
- `access_key`: 44-digit chave de acesso (legally required unique ID)
- `authorization_protocol`: SEFAZ authorization number
- `qr_code_url`: Direct link for mobile verification
- `danfe_url`: Human-readable PDF version

**Compliance Notes**:
- NF-e XML is the legal document
- DANFE PDF is the auxiliary document for visual verification
- Access key enables instant SEFAZ verification via QR code

---

### 🇪🇺 European Union - VAT Directive

**Legal Requirement**: VAT invoices must contain specific fields (Directive 2006/112/EC) including VAT identification numbers, tax breakdowns by rate.

**ORF Integration Strategy**: Embed VAT-specific requirements in `extensions.eu_vat`

**Example**:
```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "ORF-EU-DE-2024-001",
    "issue_timestamp": "2024-12-28T15:20:00+01:00",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "Beispiel Einzelhandel GmbH",
    "country_code": "DE",
    "tax_identifiers": [
      {
        "scheme": "EU_VAT",
        "value": "DE123456789"
      }
    ]
  },
  "customer": {
    "legal_name": "Acheteur Français SARL",
    "tax_identifiers": [
      {
        "scheme": "EU_VAT",
        "value": "FR987654321"
      }
    ]
  },
  "line_items": [
    {
      "description": "Industrial Machine Parts",
      "quantity": 10,
      "unit_price": 50.00,
      "net_amount": 500.00,
      "line_total": 595.00
    }
  ],
  "tax": {
    "tax_scheme": "EU_VAT",
    "tax_lines": [
      {
        "rate": 0.19,
        "base_amount": 500.00,
        "tax_amount": 95.00,
        "jurisdiction": "DE"
      }
    ]
  },
  "total": 595.00,
  "currency": "EUR",
  "extensions": {
    "eu_vat": {
      "invoice_type": "COMMERCIAL_INVOICE",
      "member_state": "DE",
      "reverse_charge": false,
      "intra_community_supply": true,
      "vat_breakdown": [
        {
          "rate": 0.19,
          "taxable_amount": 500.00,
          "vat_amount": 95.00,
          "category": "STANDARD_RATE"
        }
      ],
      "payment_terms": "NET_30",
      "invoice_currency_exchange_rate": null
    }
  }
}
```

**Key Fields**:
- `reverse_charge`: Indicates if customer must self-assess VAT
- `intra_community_supply`: Cross-border B2B transaction within EU
- `vat_breakdown`: Detailed breakdown by rate category

**Compliance Notes**:
- Supports both B2C (standard VAT) and B2B (reverse charge) scenarios
- Handles intra-EU supplies with proper VAT treatment
- Member state identification for jurisdiction clarity

---

### 🇺🇸 United States - State Sales Tax

**Legal Requirement**: Varies by state; some require specific fields for sales tax reporting (nexus, jurisdiction codes).

**ORF Integration Strategy**: Embed state-specific tax information in `extensions.us_sales_tax`

**Example**:
```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "ORF-US-TX-2024-001",
    "issue_timestamp": "2024-12-28T10:15:00-06:00",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "Texas Retail Store LLC",
    "country_code": "US",
    "tax_identifiers": [
      {
        "scheme": "US_EIN",
        "value": "12-3456789"
      },
      {
        "scheme": "TX_TAXPAYER_NUMBER",
        "value": "12345678901"
      }
    ]
  },
  "line_items": [
    {
      "description": "Office Chair",
      "quantity": 1,
      "unit_price": 200.00,
      "line_total": 200.00
    }
  ],
  "tax": {
    "tax_scheme": "US_SALES_TAX",
    "tax_lines": [
      {
        "rate": 0.0625,
        "base_amount": 200.00,
        "tax_amount": 12.50,
        "jurisdiction": "US-TX-STATE"
      },
      {
        "rate": 0.01,
        "base_amount": 200.00,
        "tax_amount": 2.00,
        "jurisdiction": "US-TX-AUSTIN"
      }
    ]
  },
  "total": 214.50,
  "currency": "USD",
  "extensions": {
    "us_sales_tax": {
      "state": "TX",
      "local_jurisdiction": "Austin",
      "jurisdiction_code": "453-2501-3",
      "nexus": true,
      "tax_breakdown": [
        {
          "type": "STATE",
          "rate": 0.0625,
          "amount": 12.50
        },
        {
          "type": "LOCAL",
          "rate": 0.01,
          "amount": 2.00
        }
      ],
      "exemption_certificate": null,
      "streamlined_sales_tax": false
    }
  }
}
```

**Key Fields**:
- `nexus`: Indicates if merchant has physical/economic presence in state
- `jurisdiction_code`: Specific local tax jurisdiction
- `exemption_certificate`: Reference to tax exemption (if applicable)

**Compliance Notes**:
- Handles multi-tier taxation (state + local)
- Supports nexus determination for online sellers
- Can reference exemption certificates for B2B/non-profit sales

---

## Multi-Jurisdiction Example

**Scenario**: A merchant operating globally needs to issue a single receipt that complies with multiple tax systems simultaneously.
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
      "description": "Cloud Services - Monthly Subscription",
      "quantity": 1,
      "unit_price": 1000.00,
      "line_total": 1000.00
    }
  ],
  "tax": {
    "tax_scheme": "MULTI",
    "tax_lines": [
      {
        "rate": 0.20,
        "base_amount": 1000.00,
        "tax_amount": 200.00,
        "jurisdiction": "GB-VAT"
      }
    ]
  },
  "total": 1200.00,
  "currency": "USD",
  "extensions": {
    "eu_vat": {
      "member_state": "GB",
      "reverse_charge": true,
      "invoice_type": "COMMERCIAL_INVOICE"
    },
    "us_sales_tax": {
      "state": "CA",
      "nexus": false,
      "note": "Digital service - not subject to CA sales tax"
    },
    "br_nfe": {
      "note": "Not applicable - service delivered outside Brazil"
    }
  }
}
```

---

## Implementation Guidelines

### For POS Vendors

1. **Generate Both Formats**: Create jurisdiction-required document (CFDI XML, NF-e XML, etc.) AND ORF JSON
2. **Embed References**: Include links/IDs to official documents in `extensions`
3. **Preserve Legal Documents**: Never replace official formats with ORF
4. **Support Multi-Jurisdiction**: Allow multiple `extensions` to coexist

### For App Developers

1. **Parse Core Fields**: Always read standard ORF fields first
2. **Check Extensions**: Look for jurisdiction-specific data in `extensions`
3. **Graceful Degradation**: Handle missing `extensions` gracefully
4. **Respect Legal Requirements**: When displaying receipts, show links to official documents

### For Merchants

1. **Compliance First**: Always generate legally-required documents
2. **ORF as Bonus**: Offer ORF receipts as user-friendly supplement
3. **Link Both**: Provide easy access to both ORF (for apps) and official documents (for audits)

---

## Future Jurisdictions

This document will be expanded to include:

- 🇨🇳 China - Fapiao
- 🇮🇳 India - GST e-Invoice
- 🇯🇵 Japan - Qualified Invoice
- 🇦🇺 Australia - Tax Invoice
- 🇰🇷 South Korea - E-Tax Invoice

**Community Contributions Welcome**: Submit PRs with your jurisdiction's requirements!

---

## Legal Disclaimer

ORF is a technical standard and does not provide legal advice. Merchants must ensure compliance with local tax and invoicing laws. This document provides technical guidance for integration but does not constitute legal or tax advice.

**Always consult with local tax authorities or legal counsel for compliance requirements.**
