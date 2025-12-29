# 📡 OpenRF Specification
[← Back to Official Site](https://openreceiptformat.github.io/orf-spec/)

<img width="261" height="273" alt="ORF-Icon" src="https://github.com/user-attachments/assets/e72ef7ff-0e6d-4acc-850e-aedfde907903" />

# orf-spec
The Open Receipt Format (ORF) is an open, extensible data standard for digital receipts that unifies global receipt practices while preserving local compliance requirements.

# Mission statement:

Our mission is to create a vendor-neutral, extensible specification for digital receipts that supports global tax regimes, industry practices, and future innovation while remaining human-readable and machine-verifiable.

It is **Not** a replacement for national tax standards
**Not** a rendering format (PDF / print / HTML)

It is **A semantic interchange format**

Composable, optional, and jurisdiction-aware

# 🧾 Open Receipt Format (ORF)

> A privacy-first, open standard for digital receipts

[![License: CC0-1.0](https://img.shields.io/badge/License-CC0%201.0-lightgrey.svg)](http://creativecommons.org/publicdomain/zero/1.0/)
[![GitHub stars](https://img.shields.io/github/stars/openreceiptformat/orf-spec.svg?style=social&label=Star)](https://github.com/openreceiptformat/orf-spec)

## The Problem

250 billion paper receipts are printed annually worldwide. They:
- Contain toxic BPA and can't be recycled ♻️
- Fade within weeks, making returns difficult
- Cost merchants $500-2,000/year in paper and printer maintenance
- Are easily lost, creating tax/accounting headaches

Current "digital receipt" solutions require:
- Creating accounts at every store
- Uploading sensitive purchase data to the cloud
- Using proprietary apps that don't interoperate

## The Solution

**Open Receipt Format (ORF)** is an open, standardized JSON format that enables:

✅ **Privacy-first design** - Receipts stored locally on user's device  
✅ **Interoperability** - Any app can read any ORF receipt  
✅ **Merchant-friendly** - Easy to generate from existing POS systems  
✅ **Future-proof** - Extensible schema supports new features  

## Quick Example
```json
{
  "orf_version": "1.0",
  "receipt_id": "a3f2b1c0-...",
  "merchant": {
    "name": "The Coffee Shop",
    "location": {
      "city": "San Francisco",
      "postal_code": "94102"
    }
  },
  "items": [
    {
      "name": "Cappuccino",
      "quantity": 1,
      "unit_price": 4.50,
      "total_price": 4.50
    }
  ],
  "totals": {
    "subtotal": 4.50,
    "tax": 0.41,
    "total": 4.91
  },
  "transaction": {
    "timestamp": "2024-12-28T09:15:00Z",
    "currency": "USD"
  }
}
```

## Documentation

📖 **[Read the Full Specification](spec/ORF-v1.0.md)**  
📋 **[See Example Receipts](spec/examples/)**  
🔧 **[Implementation Guide](spec/implementation-guide.md)** *(coming soon)*

## Get Involved

We're building this in the open and welcome contributions:

- 💬 **[Join the Discussion](https://github.com/openreceiptformat/orf-spec/discussions)** - Share ideas and ask questions
- 🐛 **[Report Issues](https://github.com/openreceiptformat/orf-spec/issues)** - Found something unclear?
- 🤝 **[Contribute](CONTRIBUTING.md)** - Help improve the spec
- ⭐ **Star this repo** - Show your support!

## Who's Using ORF?

**Consumer Apps:**
- [The Ledger](https://github.com/yourusername/ledger) - Reference implementation
- *Your app here - submit a PR!*

**POS Integrations:**
- *Coming soon*

## Working Group

The ORF specification is maintained by a volunteer working group:
- **Mike Aguilar** - Founder & Spec Lead
- *Open positions - [Join us!](https://github.com/openreceiptformat/orf-spec/discussions)*

## License

The Open Receipt Format specification is released under [CC0 1.0 Universal](LICENSE) - completely open, no restrictions.

Implementation code (libraries, tools) may use different licenses as appropriate.

---

**Website:** [openreceiptformat.org](https://openreceiptformat.org) *(coming soon)*  
**Contact:** hello@openreceiptformat.org *(coming soon)*

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
