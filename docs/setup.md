# Setup and configuration

These sanitized exports preserve the workflow structure and mapped data tokens but intentionally remove private IDs and embedded candidate data. **Expect to reselect connections and resources within Make**; placeholders such as `RESELECT_SPREADSHEETID` are not real IDs. Due to Make-specific actor input field names, the actor-selection module may need its Input JSON re-entered after selecting the actor.

1. Import `scenario-1-job-discovery.json` as a new Make scenario. Reconnect Apify and select your authorized actor. Verify the actor's input JSON, asynchronous mode, estimated cost, and daily/weekday schedule. Confirm success webhook routing.
2. Create a Make Data Store named `Indeed_Processed_Job_IDs` (key-only records suffice); select it in both Data Store modules in Scenario 2.
3. Create a Google Sheet with a `Job Postings` tab and this exact header order:

   `Job ID, Date Found, Job Title, Company, Location, Remote Eligibility, Salary, Job URL, Job Description, Match Score, Matching Qualifications, Missing Qualifications, AI Recommendation, Application Status, Cover Letter URL, Date Applied, Notes`

4. Create a Google Docs template with these placeholders: `{{date}}`, `{{company}}`, `{{job_title}}`, `{{cover_letter_body}}`. Configure an output folder.
5. Import `scenario-2-ai-qualification.json`, reconnect Apify, Google, OpenAI, the actor-run webhook, the Data Store, spreadsheet/tab, Docs template, and output folder. Revalidate all mappings and filters.
6. **Locally** replace both `VERIFIED CANDIDATE PROFILE` placeholders with truthful private experience and portfolio evidence. Do not commit resumes, phone numbers, candidate profiles, API keys, or generated cover letters.
7. Ensure the stored data ID is the same `{{2.id}}` used in both Data Store operations and the Google Sheets search.
8. Check all mapped OpenAI fields (`result.match_score`, `result.remote_eligibility`, `result.hard_requirements`, etc.) against actual outputs; the original setup relied on the response JSON being exposed as an object.
9. Test with synthetic records without triggering scraping. Verify unknown job IDs pass, known IDs stop, low scores stop, and a simulated qualified record can add a row, create a Google Doc, and update Column O.
10. Enable **Store incomplete executions**, inspect failure-retry behavior, and turn on the webhook-watching scenario before enabling the weekday scraper.

## Configuration notes observed in the export

- `Remote Jobs Only` tests **exact case-insensitive equality** `location = Remote`, not whether the location *contains* `Remote`. Adjust if the actor returns compound location values, while separately enforcing Canadian/Ontario eligibility in the AI output.
- Google Sheets `Add a Row` uses `USER_ENTERED` in the uploaded source, not `RAW`. Consider switching to `RAW` to avoid untrusted text being interpreted as formulas; verify numeric score and date display afterward.
- Both OpenAI modules have `store = true` and `createConversation = true` in the uploaded source. Review data retention and cost preferences before deploying, especially when processing candidate information and scraped job descriptions.
- The original `Get Dataset Items` limit is 100 despite the actor's 20-items-per-search configuration. This is a retrieval cap, not a command to buy 100 results.
- The serialized Make blueprint does not prove the weekday scheduling configuration, live permissions, secrets handling, or the scenario-level incomplete-execution switch. Reconfirm these in Make.
- Recording job IDs before the downstream Google Docs and Sheets operations creates a retry/recovery requirement. Review incomplete executions carefully; do not silently discard failures.

## Cost controls

The chosen 20-result actor setting was estimated at $0.12 per actor run in the original project, and 20 weekday runs at $2.40 monthly, excluding Make and OpenAI usage. Actual usage will differ by actor pricing, number of weekdays, deduplication and retries.
