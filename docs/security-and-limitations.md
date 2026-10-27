# Security and limitations

## Sanitization performed

- Replaced numeric Make connection identifiers and actor-run webhook identifiers.
- Replaced Data Store IDs, Google Spreadsheet IDs, Google Docs template IDs, and destination folder IDs with explicit reconfiguration placeholders.
- Replaced account-specific connection labels and removed setup-validation metadata.
- Replaced embedded personally identifiable candidate history and detailed professional profile with clearly marked private-profile placeholders.
- Preserved module order, mapping expressions, operator definitions, filters, and business-logic wiring.

No live API token or password field was found by basic structural/string inspection. **This is not a guarantee that the source JSON is safe**; do not publish original exports, execution logs, resumes, or live job datasets.

## Known limitations

- Sanitized JSON is a reproducible structural reference rather than an instantly executable deployment. Connections, actor input field names, webhooks, Sheets, Docs, and Data Store must be reconfigured after importing.
- The scraper's access rights and Indeed terms are not established by these files. Verify permissible data collection independently.
- A hard `location = Remote` test can exclude valid remote jobs with more descriptive locations.
- AI grading can make mistakes, including confusing required and preferred credentials. Manual review remains essential.
- The historical store blocks reruns of rejected postings, but could also prevent automatic recovery if downstream Sheets/Docs work fails after the ID is recorded. Preserve and review incomplete executions.
- Repeated runs may re-score a listing when it has a changing or missing job identifier; use stable ID normalization.
- Formula injection is possible when third-party job text is written to Google Sheets as `USER_ENTERED`; consider `RAW`.
- There is no automatic job application submission and no demonstrated real-world placement result.

## Public repository safety checklist

Before pushing, scan exported blueprints and screenshots for email addresses, API keys, bearer tokens, webhook URLs, Make connection IDs, document IDs, Data Store IDs, and candidate or employer private data. Use a secret scanning tool and manually review diffs. If any real secret is accidentally committed, revoke/rotate it and remove it from repository history. Merely deleting a committed file does not remove historical exposure.
