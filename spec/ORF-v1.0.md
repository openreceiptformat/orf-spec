
<img width="261" height="273" alt="ORF-Icon" src="https://github.com/user-attachments/assets/e655fee8-1d4d-4e62-863b-bf42b58ab087" />

# Open Receipt Format (ORF) Specification

**Version:** 1.0  
**Status:** Draft  
**Last Updated:** 2024-12-28

## Table of Contents

1. [Introduction](#introduction)
2. [Design Principles](#design-principles)
3. [Core Schema](#core-schema)
4. [Field Definitions](#field-definitions)
5. [Implementation Guidelines](#implementation-guidelines)
6. [Security Considerations](#security-considerations)
7. [Examples](#examples)
8. [International Compliance](international-compliance.md)

## Introduction

The Open Receipt Format (ORF) is a standardized, JSON-based format for digital receipts. It enables interoperability between merchants, point-of-sale systems, and consumer applications while respecting user privacy.

### Goals

- Eliminate paper receipt waste
- Provide structured, machine-readable transaction data
- Enable privacy-preserving receipt storage
- Support merchant innovation (loyalty, analytics, etc.)

### Non-Goals

- Payment processing (handled by existing rails)
- User authentication (receipts are bearer tokens)
- Regulatory compliance (region-specific requirements handled separately)

## Design Principles

1. **Privacy-first**: No required user identifiers
2. **Merchant-friendly**: Easy to generate from existing POS data
3. **Extensible**: Support future features without breaking changes
4. **Human-readable**: JSON format, sensible field names
5. **Minimal**: Required fields only; everything else optional

## Core Schema
### Full Schema
See schema.json
```

### Minimal Valid ORF Receipt
```json
{
  "orf_version": "0.1.0",
  "receipt": {
    "receipt_id": "MIN-2024-001",
    "issue_timestamp": "2024-12-28T09:15:30Z",
    "receipt_type": "sale"
  },
  "merchant": {
    "legal_name": "The Coffee Shop",
    "country_code": "US"
  },
  "line_items": [
    {
      "description": "Cappuccino",
      "quantity": 1,
      "unit_price": 4.50,
      "line_total": 4.50
    }
  ],
  "total": 4.91,
  "currency": "USD"
}

```
### Field Definitions**

**receipt_id** (required)
- **Type**: String
- **Description**: Unique identifier for this receipt
- **Format**: UUID recommended, but any unique string accepted
- **Example**: `"a3f2b1c0-9d7e-4f5a-b8c2-1e3d4f5a6b7c"`

**orf_version** (required)
- **Type**: String
- **Description**: Version of ORF spec this receipt follows
- **Format**: Semantic versioning (major.minor)
- **Example**: `"1.0"`

**merchant.name** (required)
- **Type**: String
- **Description**: Human-readable merchant name
- **Example**: `"The Coffee Shop"`

**transaction.timestamp** (required)
- **Type**: String
- **Format**: ISO 8601 with timezone
- **Example**: `"2024-12-28T14:30:00-08:00"`

**transaction.currency** (required)
- **Type**: String
- **Format**: ISO 4217 currency code
- **Example**: `"USD"`, `"EUR"`, `"GBP"`

**items[].quantity** (required)
- **Type**: Number
- **Description**: Quantity of this item purchased
- **Note**: Can be decimal for weight-based items (e.g., 0.5 kg)

**totals.** (required)
- **Type**: Number
- **Format**: Decimal number, two decimal places recommended
- **Note**: `subtotal + tax + tip - discount = total`

## Implementation Guidelines

### For POS Systems

**Generating ORF receipts:**

1. After transaction completion, serialize transaction data to ORF JSON
2. Assign unique `receipt_id` (UUID recommended)
3. Include all available data, but only required fields are mandatory
4. Make receipt available via:
   - NFC tag (NDEF format)
   - QR code (URL pointing to receipt endpoint)
   - API endpoint for programmatic access
   - Email/SMS with embedded JSON or link

**Storage recommendations:**
- Retain receipts for minimum 90 days
- Longer retention for high-value items
- Comply with local data retention laws

### For Consumer Applications

**Parsing ORF receipts:**

1. Validate `orf_version` is supported (currently only "1.0")
2. Check all required fields are present
3. Gracefully handle unknown fields (forward compatibility)
4. Store locally by default (privacy preservation)
5. Validate currency codes, country codes against standards

**Storage recommendations:**
- Store receipts in local database or file system
- Use `receipt_id` as primary key
- Index by timestamp, merchant, total for quick retrieval
- Implement search across items, merchants, categories

## Security Considerations

### Privacy

- No Personally Identifiable Information (PII) required in base format
- `receipt_id` should not be derivable from transaction details
- Payment details should be minimal (last 4 digits only)
- Merchants should not embed tracking identifiers
- Treat customer as:
-- Optional
-- Jurisdiction-triggered
-- Explicitly non-identifying by default

No core field should imply identity.

### Transmission

- Use HTTPS for any API endpoints serving receipts
- Consider encryption for NFC transmission (though typically short-range is sufficient)
- Implement rate limiting on receipt retrieval endpoints

### Authenticity

- Consider digital signatures for high-value receipts
- Use HMAC or JWT for receipt authenticity verification
- Merchant certificates could validate receipt origin

## Examples

See the [examples directory](examples/) for complete receipt samples:
- [coffee-shop.json](examples/coffee-shop.json) - Simple transaction
- [restaurant.json](examples/restaurant.json) - With tip and modifiers
- [retail.json](examples/retail.json) - With return policy

## Versioning

ORF uses semantic versioning:
- **Major version** (1.x): Breaking changes, old parsers won't work
- **Minor version** (x.0): New optional fields, backward compatible

## License

This specification is released under CC0 1.0 Universal. Anyone may implement without restriction.

## Changelog

- **v1.0 (2024-12-28)**: Initial draft release

## Contributors

- Mike Aguilar - Founder & Spec Lead
- [Contributors will be listed as they join]

---

**Questions?** Open an issue or start a discussion on GitHub.
