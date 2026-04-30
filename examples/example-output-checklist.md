Part 1.

Claude responded: I've received the Payment Provider QA Checklist Generator v2 prompt.
I've received the Payment Provider QA Checklist Generator v2 prompt. This is a system/instruction document that defines how I should behave when you provide me with payment provider documentation and an implementation task.

To generate a QA checklist and reference documentation, I need you to provide the actual inputs in this format:

<documentation>
[Provider API documentation text, PDF content, or public URL]
</documentation>

<task>
[Ticket / user story / implementation task]
</task>

<config>
mode=focused | full
language=en | ru
</config>
Please share:

Documentation — the payment provider's API docs (paste the text, attach a PDF, or provide a public URL I can fetch)
Task — the ticket, user story, or implementation scope (e.g., "Implement DEPOSIT for scheme NIK via Provider X")
Config — your preferred mode (focused for in-scope only, full for broader coverage) and language (en or ru)
Once you provide these, I'll produce the full output: provider/task summary, provider-specific checklist, reference sheet (HTTP codes, errors, amounts, statuses), optional full operation coverage, and a Confluence-ready summary table — strictly grounded in the docs you supply, with TBC where things are unclear.

Part 2
