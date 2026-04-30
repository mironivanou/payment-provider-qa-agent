# Payment Provider QA Agent

AI-assisted QA toolkit for payment provider integrations.

This project helps QA engineers analyse payment provider API documentation and implementation tasks, then generate:

- provider-specific QA checklists
- provider reference sheets
- Confluence-ready summaries
- Qase-compatible CSV imports

## Why this exists

Payment provider integrations often include provider-specific flows, statuses, webhooks, signatures, amount formats, restrictions, and error mappings.

This toolkit helps reduce manual analysis time and keeps QA coverage structured and consistent.

## Workflow

1. Paste provider API documentation.
2. Paste implementation task or ticket.
3. Run Prompt 01: Provider QA Checklist Generator.
4. Review generated checklist.
5. Run Prompt 02: Qase CSV Exporter.
6. Import generated CSV into Qase.

## Repository structure

```text
payment-provider-qa-agent/
├── prompts/
│   ├── 01-provider-qa-checklist-generator.md
│   └── 02-qase-csv-exporter.md
├── examples/
│   ├── example-input.md
│   ├── example-output-checklist.md
│   └── example-qase-import.csv
├── templates/
│   └── confluence-provider-summary.md
└── docs/
    └── workflow.md
Prompts
Provider QA Checklist Generator
Qase CSV Exporter
Example

See the /examples folder for sample input, generated checklist, and Qase CSV output.

Use case

This toolkit is designed for QA engineers working with:

PSP integrations
payment gateways
alternative payment methods
hosted payment pages
webhooks
refunds and withdrawals
Qase test management
Author

Created as a practical QA automation support toolkit for payment provider integration testing.
