# Groww Review Intelligence and Approval

An n8n workflow that turns a CSV of public Groww app-review excerpts
into a structured weekly product pulse and, when supported by evidence,
a fee-clarification section. It pauses for human approval before
updating a Google Doc and creating a Gmail draft.

> **Milestone status:** Built and tested with a small seed dataset.
> Expand the review coverage and verify source records before treating
> findings as representative research.

## What it does

1.  Downloads a CSV from Google Drive.
2.  Extracts and prepares review records.
3.  Uses an OpenAI model to analyse review themes.
4.  Parses and validates the analysis.
5.  Generates a weekly product pulse (target: at most 250 words) and a
    fee explainer only when review evidence supports a recurring fee
    misunderstanding.
6.  Validates the deliverables and presents an approval form.
7.  If approved, updates the Google Doc **Groww Weekly Product Pulse
    Log** and creates a Gmail draft. It does **not** send the email.

## Workflow overview

``` text
Manual Trigger
    |
Google Drive: Download CSV
    |
Extract From File (CSV)
    |
Validate and Prepare Reviews
    |
OpenAI: Analyse Reviews
    |
Parse and Validate Analysis
    |
OpenAI: Generate Pulse and Fee Explainer
    |
Validate Deliverables
    |
Wait For Approval
    |
IF Decision == "Approve"
    |                         |
Google Docs: Update       Gmail: Create Draft
```

The reject path should not connect to Google Docs or Gmail write
actions.

## Suggested repository structure

``` text
.
├── README.md
├── workflow/
│   └── groww-review-intelligence-and-approval.json
├── data/
│   └── groww_public_reviews_seed_sample.csv
├── sources/
│   └── sources.md
└── docs/
    └── screenshots/
```

Export the workflow JSON from n8n and place it in `workflow/`. Include
the CSV and source list only if you have permission to redistribute them
and they contain no sensitive information.

## Prerequisites

-   An n8n instance with the required nodes.
-   Google Drive access to the input CSV.
-   Google Docs edit access to the destination document.
-   Gmail access for creating drafts.
-   An OpenAI credential and a model available to your account.

Configure credentials inside n8n. Do not commit API keys, OAuth tokens,
passwords, or credential exports.

## Setup

1.  Export the workflow JSON from n8n.
2.  Import it into your target n8n instance.
3.  Configure Google Drive, Google Docs, Gmail, and OpenAI credentials.
4.  Update file and document selections if the imported workflow refers
    to resources in another workspace.
5.  Upload the input CSV to Google Drive and select it in the download
    node.
6.  Confirm the destination Google Doc is **Groww Weekly Product Pulse
    Log**, or choose a document you control.
7.  Confirm the IF condition checks `{{ $json.Decision }}` for equality
    with `Approve`.
8.  Confirm both Google Docs Update and Gmail Create Draft connect only
    to the **true/Approve** output.
9.  Test both approval outcomes and inspect the execution path before
    publishing.

Imported workflows may require credentials to be reconfigured. Exporting
the workflow does not grant access to its Google Drive files, Google
Doc, or Gmail account.

## Approval and safety checks

-   **Approve:** Update the Google Doc and create a Gmail draft.
-   **Reject:** No Google Doc update or Gmail draft should be created by
    that execution.
-   **Email behavior:** Creates a draft only; it does not send email
    automatically.
-   **Before sharing:** Inspect the exported JSON for secrets, private
    data, or sensitive configuration.

## Data and research limitations

The milestone was tested with `groww_public_reviews_seed_sample.csv`, a
seed dataset of **8 review excerpts** dated from **18 August 2026 to 18
September 2026**. This is roughly one month of coverage, not the 8--12
weeks needed for a stronger weekly-trend analysis.

Therefore:

-   Findings are exploratory and should not be presented as
    representative of all Groww users.
-   Theme counts and sentiment can change materially with a larger
    sample.
-   Verify review excerpts and source metadata against the original
    public listing before publication.
-   Do not invent user quotes, review counts, fee rules, or
    official-source claims.
-   If the supplied reviews do not establish a recurring fee
    misunderstanding, the fee explainer should say that it is **not
    applicable** rather than speculate.

For a stronger analysis, expand the collection to 8--12 weeks, preserve
source URLs and collection dates, remove duplicates, and include up to
three genuine verbatim quotes traceable to source records.

## Official reference links

-   [Groww Pricing and Charges](https://groww.in/pricing)
-   [Groww Help: What fees does Groww
    charge?](https://groww.in/help/payments-%26-withdrawals/payments-charges/what-fees-does-groww-charge--58)
-   [Groww app on Google
    Play](https://play.google.com/store/apps/details?id=com.nextbillion.groww)
-   [n8n workflow sharing
    documentation](https://docs.n8n.io/workflows/sharing/)

Recheck official links when the workflow runs. A recorded `Last checked`
date does not by itself prove that live verification occurred.

## Validation checklist

-   [ ] CSV loads and the expected record count is visible.
-   [ ] Review text, dates, and source metadata are preserved correctly.
-   [ ] No more than five themes are returned, with the top three
    highlighted.
-   [ ] Any quoted reviews are verbatim and traceable to source records.
-   [ ] Weekly pulse is at most 250 words.
-   [ ] Fee explainer contains no more than six bullets and uses two
    official sources when applicable.
-   [ ] Unsupported fee claims are omitted.
-   [ ] `Approve` updates the intended Google Doc and creates a Gmail
    draft.
-   [ ] `Reject` performs neither write action.
-   [ ] The Gmail draft is reviewed by a human and is not sent
    automatically.
-   [ ] Workflow export contains no secrets before it is committed or
    shared.
-   [ ] Dataset coverage and limitations are documented accurately.

## Disclaimer

This is an educational product-analytics automation milestone, not
investment advice, an official Groww statement, or a statistically
representative customer-research report. Verify findings and current fee
information against original sources before relying on or publishing
them.

## License

Add a license only if you have the rights to license all code and data
in the repository. Public review text and third-party trademarks may
have separate terms.
