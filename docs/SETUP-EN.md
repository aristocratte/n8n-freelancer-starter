# n8n Freelancer Starter: setup guide (EN)

Two free n8n workflows for freelancers who invoice businesses in the EU or the UK. Tested on n8n 2.22.6, self hosted.

| # | Workflow | What it does | Credentials |
|---|---|---|---|
| 01 | Client VAT and company check | Checks an EU VAT number on the official VIES service and returns the company name, address, a block to paste on your invoice and a reverse charge hint | None (free public API) |
| 03 | Revenue and tax threshold tracker | Adds up the cash you received this year in any currency (free ECB based FX rates), compares it to your VAT or tax regime limits and estimates contributions to set aside | None (free public API) |

## 1. Install n8n

Use n8n Cloud, or self host with `npx n8n` (Node.js 22+) or Docker: `docker run -it --rm -p 5678:5678 n8nio/n8n`.

## 2. Import

In n8n, go to Workflows, then Import from File, and pick each file in `workflows/`. Then switch the workflow to Active.

## 3. Test

Both workflows start with a Webhook. Sample data is in `tests/`.

```bash
curl -X POST http://localhost:5678/webhook/client-vat-check -H 'Content-Type: application/json' -d '{"vat":"IE6388047V"}'
curl -X POST http://localhost:5678/webhook/revenue-threshold-tracker -H 'Content-Type: application/json' -d @tests/sample-payments.json
```

Expected outputs: `tests/t1-valid-IE.json` for the VAT check and `tests/t3.json` for the tracker.

## 4. How it behaves

- WF01 rejects badly formatted numbers before calling VIES (status `INVALID_FORMAT`).
- If VIES is down for a country, WF01 returns `VIES_UNAVAILABLE` instead of saying the number is invalid. Just try again later.
- Possible statuses: `VALID`, `NOT_VALID`, `INVALID_FORMAT`, `VIES_UNAVAILABLE`.

## 5. Configure

- WF03: open the Code node "Compute YTD & alerts". All limits and rates sit in the `PRESETS` block at the top: `FR_MICRO_BNC`, `FR_MICRO_BIC_SERVICES`, `UK_SOLE_TRADER` and `CUSTOM`. Edit them or add your own country. Choose one by sending `"preset"` in the request.
- Payment input format: `{"preset":"FR_MICRO_BNC","payments":[{"amount":1200,"currency":"EUR","paidDate":"2026-03-15"}]}`.

## 6. Go further

- Replace the Webhook with a Schedule trigger and read payments from Google Sheets or Airtable.
- Send the WF03 summary to Telegram, Slack or email once a month.

A full kit with late payment reminders is coming soon on Gumroad.

## Disclaimer

Rates and thresholds change. Check them on official sources (EU VIES, ECB, your tax office). This is not legal or tax advice.
