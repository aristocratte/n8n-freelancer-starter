# n8n Freelancer Starter (EU VAT check + tax threshold tracker)

Two free n8n workflows for freelancers who invoice businesses in Europe or the UK.

1. **Client VAT and company check** (`workflows/01-client-vat-check.json`)  
   Paste an EU VAT number. Queries the official VIES service and returns validity, company name and address. Bad formats are caught before the API call. Handles per-country VIES downtime.

2. **Revenue and tax threshold tracker** (`workflows/03-revenue-threshold-tracker.json`)  
   Sums year-to-date cash received (multi-currency, free ECB-based FX) and warns at 80%, 95% and 100% of your VAT or tax-regime limit. Presets for France and the UK, or set your own.

## Requirements

- n8n 2.x (cloud or self-hosted)
- No paid APIs, no credentials to connect

## Quick start

1. Import a JSON file from `workflows/` into n8n (Workflows → Import from File).
2. Open the first Set / Manual Trigger node and put your sample input (examples in `tests/`).
3. Follow `docs/SETUP-EN.md` or `docs/SETUP-FR.md`.

## License

MIT (see `LICENSE`).

## Packaged version

A zip with the EN/FR setup guide is also on Gumroad (free): https://ariscrd.gumroad.com/l/gohupb

A paid kit adds automatic late-payment reminders (legal interest + €40 recovery fee).

## Disclaimer

Not legal or tax advice. Rates and thresholds change. Verify them on official sources (VIES, ECB, your tax office) at least once a year.
