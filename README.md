# Invoice Parser

Receives extracted invoice text, finds common German invoice fields and returns a JSON result with validation messages.

**Flow:** Webhook -> Prepare Text -> Extract Fields -> Check Invoice -> IF -> Respond

## Run

Import `workflow.json` and click **Listen for test event** in the webhook node. Use PowerShell:

```powershell
$invoice = Get-Content .\02-invoice-parser\sample_invoice.txt -Raw
$body = @{ text = $invoice } | ConvertTo-Json
Invoke-RestMethod -Uri "http://localhost:5678/webhook-test/invoice-parser" -Method Post -ContentType "application/json" -Body ([Text.Encoding]::UTF8.GetBytes($body))
```

Change the path if you run the command from a different folder. The body must be `{ "text": "..." }`.

## Fields

`supplier`, `invoice_no`, `invoice_date`, `gross_eur`, `vat_rate`, `vat_eur`, `iban`, `confidence`, `status`, `errors`, and `warnings`.

## Notes

- Input must be **text** (for example, OCR output). The workflow does not read PDFs or images directly.
- The parser is tuned for invoices using German labels. It is a rule-based example, not a universal invoice parser.
- An invalid IBAN is flagged with a checksum check. A missing IBAN is only a warning.
- Never approve a payment using extracted fields without checking the original invoice.
