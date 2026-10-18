# Invoice & Payment ID Validator — remote MCP server

Check payee identifiers **before an AI agent pays an invoice or saves supplier bank details**: IBAN, BIC/SWIFT, EU VAT (with optional live VIES lookup), UK VAT and US EIN.

This is a **hosted (remote) MCP server** using Streamable HTTP. There is nothing to install or run locally, and this repository holds documentation and the MCP Registry manifest only. The server code is not open source.

| Endpoint | Auth | Limits |
|---|---|---|
| `https://invoice-validator.mohamed-bahaa53000.workers.dev/demo/mcp` | none | free, 10 tool calls per IP per UTC day, no batch |
| `https://invoice-payment-id-validator.p.rapidapi.com/mcp` | `X-RapidAPI-Key` + `X-RapidAPI-Host` headers | paid per request via [RapidAPI](https://rapidapi.com/MohamedBahaa/api/invoice-payment-id-validator) (free Basic plan available) |

Official MCP Registry name: `dev.workers.mohamed-bahaa53000.invoice-validator/invoice-payment-id-validator`

## Tools

- `validate_iban`: mod-97 checksum, length for its country (100+ countries), SEPA membership; returns normalized/formatted IBAN, country, BBAN, bank/branch/account parts where known, or machine-readable error codes.
- `validate_bic`: BIC/SWIFT structure (8 or 11 chars), ISO country, test-BIC flag; pass `iban` to check the BIC country matches the IBAN country.
- `validate_eu_vat`: EU VAT ID (27 member states + XI) format and check digit offline; `live=true` also queries EU VIES for registration status, name and address (VIES failures return `vies.checked=false`, never a false "invalid").
- `validate_gb_vat`: UK VAT number format, types and HMRC mod-97 / 9755 checksum.
- `validate_ein`: US EIN 9-digit format and valid IRS campus prefix (EINs have no check digit, so this catches typos and fake prefixes, not every invalid number).
- `validate_batch`: many identifiers in one call (**paid endpoint only**).

## Use cases

- Accounts-payable agents: verify supplier IBAN/BIC and VAT IDs before approving a payment run.
- Catch IBAN typos and BIC/IBAN country mismatches (a common fraud signal).
- Vendor onboarding: confirm an EU VAT ID is registered in VIES.

## Connect

Claude Desktop / Cursor / Cline / VS Code (any client that supports remote Streamable HTTP servers):

```json
{
  "mcpServers": {
    "invoice-validator": {
      "url": "https://invoice-validator.mohamed-bahaa53000.workers.dev/demo/mcp"
    }
  }
}
```

Paid / production (RapidAPI):

```json
{
  "mcpServers": {
    "invoice-validator": {
      "url": "https://invoice-payment-id-validator.p.rapidapi.com/mcp",
      "headers": {
        "X-RapidAPI-Key": "YOUR_RAPIDAPI_KEY",
        "X-RapidAPI-Host": "invoice-payment-id-validator.p.rapidapi.com"
      }
    }
  }
}
```

For clients that only support stdio: `npx -y mcp-remote https://invoice-validator.mohamed-bahaa53000.workers.dev/demo/mcp`

## Notes

- Stateless. Inputs aren't stored beyond standard request logs.
- Validation checks format and checksums (plus VIES when `live=true`). It doesn't prove an account belongs to a given company.
