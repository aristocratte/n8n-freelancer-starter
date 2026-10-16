# Freelancer Get-Paid Kit for n8n — Setup Guide (EN)

Three import-ready n8n workflows (tested on n8n 2.22.6, self-hosted) for freelancers and small agencies that invoice B2B clients, especially in the EU/UK.

| # | Workflow | What it does | Credentials needed |
|---|---|---|---|
| 01 | Client VAT & Company Check | Validates an EU VAT number on the official EU VIES service, returns legal name + address, a ready-to-paste invoice block and a reverse-charge hint | **None** (free public API) |
| 02 | Late Payment Reminders with Statutory Interest | Classifies unpaid invoices (courtesy → friendly → firm → final), computes late-payment interest + €40 flat recovery fee, writes the reminder email in EN or FR | **None** to run. Optional: SMTP/Gmail to send |
| 03 | Revenue & Tax Threshold Tracker | Sums year-to-date cash received in multiple currencies (free ECB-based FX rates), compares against VAT/regime thresholds, estimates contributions to set aside | **None** (free public API) |

## 1. Install n8n (if you don't have it)
- n8n Cloud (trial) or self-hosted: `npx n8n` (Node.js 22+) or Docker: `docker run -it --rm -p 5678:5678 n8nio/n8n`.

## 2. Import
In n8n: **Workflows → Import from File** → pick each file in `workflows/`. Then toggle **Active**.

## 3. Test in 1 minute (sample data included in `tests/`)
```bash
curl -X POST http://localhost:5678/webhook/client-vat-check -H 'Content-Type: application/json' -d '{"vat":"IE6388047V"}'
curl -X POST http://localhost:5678/webhook/late-payment-reminders -H 'Content-Type: application/json' -d @tests/sample-invoices.json
curl -X POST http://localhost:5678/webhook/revenue-threshold-tracker -H 'Content-Type: application/json' -d @tests/sample-payments.json
```
Expected outputs are in `tests/t1-*.json (t1-valid-IE.json = valid number)`, `tests/t2.json`, `tests/t3.json`.

## 4. Configure
- **WF02**: open node *Compute reminders* → `CONFIG`: `referenceRatePct` (ECB main refinancing rate, currently 2.65% as of 16 Sep 2026; update every 6 months), `marginPts` (EU directive default 8; **France: 10** per art. L441-10 Code de commerce unless your terms say otherwise), your name, tier days. B2C invoices: send `"b2b": false` (no interest/fee added).
- **WF03**: node *Compute YTD & alerts* → `PRESETS` (France micro-entrepreneur BNC/BIC, UK VAT threshold, Custom). Send `"preset"` in the request.
- Input format for invoices: `number, clientName, clientEmail, amount, currency, dueDate (YYYY-MM-DD), status (unpaid/paid), paymentLink, lang (en/fr), b2b`.

## 5. Go further (optional)
- Replace the Webhook trigger with a **Schedule** trigger + **Google Sheets / Airtable** node to read invoices automatically.
- Add a **Send Email** (SMTP) or **Gmail** node after WF02 — keep it in draft mode first. WF02 is dry-run by default: it never sends anything.
- Pipe WF03's `summary` into Telegram/Slack/email for a monthly alert.

## Disclaimer
Rates and thresholds change. Verify them on official sources (EU VIES, ECB, your tax office). Not legal or tax advice.
