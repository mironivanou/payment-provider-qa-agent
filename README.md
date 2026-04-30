# Payment Provider QA Agent
![Domain](https://img.shields.io/badge/Domain-Payments-blue)
![Type](https://img.shields.io/badge/Type-QA%20Toolkit-green)
![AI](https://img.shields.io/badge/AI-Assisted-orange)

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
```

## Prompts

- [Provider QA Checklist Generator](prompts/01-provider-qa-checklist-generator.md)
- [Qase CSV Exporter](prompts/02-qase-csv-exporter.md)

---

## Example

See the `/examples` folder for:

- sample input
- generated checklist
- Qase CSV output

---

## Example scenario

This repository includes a real-world example based on a payment flow with:

- BLIK payment method
- redirect + input flow
- provider-side field mapping differences

See `/examples` for full flow.

===

## Use case

This toolkit is designed for QA engineers working with:

- PSP integrations  
- payment gateways  
- alternative payment methods  
- hosted payment pages (HPP)  
- webhooks  
- refunds and withdrawals  
- Qase test management  

---

## Demo

Example flow:

1. Input: provider documentation + task
2. Output: provider-specific checklist
3. Output: Qase-ready CSV

See `/examples` for full example.

---
## Author

Created as a practical QA automation support toolkit for payment provider integration testing.
