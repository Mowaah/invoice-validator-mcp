# Installing the Invoice & Payment ID Validator MCP server

This is a remote (hosted) MCP server. There's nothing to clone, build or install.

1. Add this entry to the MCP settings file (for Cline: `cline_mcp_settings.json`):

```json
{
  "mcpServers": {
    "invoice-validator": {
      "url": "https://invoice-validator.mohamed-bahaa53000.workers.dev/demo/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

2. No API key is needed for the demo endpoint (10 tool calls per IP per day).
3. For production use, the user can subscribe on RapidAPI (https://rapidapi.com/MohamedBahaa/api/invoice-payment-id-validator) and switch the url to `https://invoice-payment-id-validator.p.rapidapi.com/mcp` with headers `X-RapidAPI-Key` (their key) and `X-RapidAPI-Host: invoice-payment-id-validator.p.rapidapi.com`.
4. Verify by calling `validate_iban` with `{"value": "DE89370400440532013000"}`. It should return `valid: true`.
