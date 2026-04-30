# Qase CSV Export Instructions v2

Generate a Qase-compatible CSV file for importing test cases into Qase.

Use the official Qase v1 CSV format.

Import source:
http://Qase.io CSV [deprecated]

---

## Scope Rules

Convert only in-scope test cases from the generated checklist:

- Part 2 — Provider-Specific Test Checklist
- Part 4 — Full Operation Coverage (only in-scope cases)

Exclude:

- out-of-scope cases
- informational rows
- reference tables

Do not invent new test cases to satisfy coverage.

If some expected coverage is impossible because functionality is absent, report it separately.

---

## Field Mapping

Map checklist fields to CSV columns as follows:

| Checklist Field | CSV Column |
|---|---|
| ID | id |
| Title | title |
| Why provider-specific | description |
| Steps | steps_actions |
| Expected Result | steps_result |

Priority mapping:

| Checklist | CSV |
|---|---|
| Critical | high |
| High | medium |
| Medium | low |

---

## Mandatory Headers

Use these headers exactly, in this exact order:

id,title,description,preconditions,postconditions,tags,priority,severity,type,behavior,automation,status,is_flaky,layer,steps_type,steps_actions,steps_result,steps_data,milestone_id,milestone,suite_id,suite_parent_id,suite,suite_without_cases,parameters,is_muted

Do not rename, reorder, or omit headers.

---

## Default Values

Use these defaults unless explicitly known:

| Field | Default |
|---|---|
| severity | normal |
| type | functional |
| behavior | positive |
| automation | is-not-automated |
| status | actual |
| is_flaky | no |
| layer | api |
| steps_type | classic |
| is_muted | no |

---

## Accepted Values

Use only these lowercase slug values:

### priority
high, medium, low, undefined

### severity
blocker, critical, major, normal, minor, trivial, undefined

### type
functional, smoke, regression, security, usability, performance, acceptance, compatibility, integration, exploratory, other

### behavior
positive, negative, destructive, undefined

### automation
is-not-automated, to-be-automated, automated

### status
actual, draft, deprecated

### is_flaky
yes, no

### layer
e2e, api, unit, unknown

### steps_type
classic, gherkin

---

## Tag Rules

The tags field is mandatory for every test case row.

Use comma-separated tags with no spaces.

Example:

Webhook,Amount,Deposit,BASIC_CARD

### Base tags
Add when relevant:

- Auth
- Contract
- Currency
- Amount
- Error Mapping

### Flow tags
Add when relevant:

- Webhook
- Check-State
- Dynamic Webhook
- Static Webhook
- HPP
- Signature

### Business tags
Add when relevant:

- Deposit
- Withdrawal
- Refund
- Recurring

### Payment Method tags
Add when relevant:

- BASIC_CARD
- BANKTRANSFER
- GGLP
- APPLP

Only add relevant tags.
Do not invent artificial cases for missing tags.

---

## Suite Structure Rules

Generate suites based on checklist sections.

Example suites:

- Authentication
- Deposit Flow
- Webhook Handling
- Refund Flow

### Suite rows

Suite rows must appear before test case rows.

Fill only:

- suite_id
- suite_parent_id
- suite
- suite_without_cases = 1

All other fields empty.

### Test case rows

Each test case row must reference its suite:

- suite_id
- suite_parent_id
- suite

suite_without_cases must be empty.

---

## Suite IDs

- Start at 1
- Increment sequentially
- Reuse same suite_id for cases in same suite

Do not skip numbers.

---

## Steps Format

Use classic format:

1. Step one
2. Step two

Put into:

- steps_actions
- steps_result
- steps_data (optional)

---

## CSV Formatting Rules

Generate programmatically.

Use:

Python + csv.DictWriter
quoting=csv.QUOTE_ALL

Requirements:

- UTF-8 encoding
- Unix line endings
- preserve embedded line breaks inside quoted cells
- do not create multiline CSV rows
- every row must contain all headers
- use empty string "" for unused fields

---

## ID Rules

Preserve original test case IDs from checklist.

Do not renumber.

---

## Validation Before Output

Verify:

- only in-scope cases included
- headers exact and ordered
- suite rows first
- IDs preserved
- suite IDs sequential
- tags relevant
- no hallucinated cases
- multiline steps properly quoted
