# Setup

## Before you start

You need an n8n instance (n8n Cloud or self-hosted), a Gmail account, a Google account for Sheets, and an OpenAI API key. The credentials are described in [credentials.md](credentials.md).

## Steps

1. Open n8n. Use n8n Cloud, or install it yourself by following the n8n documentation for self-hosting.
2. Create a new workflow, open the menu in the top right, choose Import from File, and select `workflow.json`.
3. Create the three credentials in [credentials.md](credentials.md) and attach them:
   - Gmail OAuth2 on **Gmail Trigger**, **Label New Enquiry**, **Label Existing Client**, **Label Job Applications**, **Label Spam**, **Label Needs Review**, **Label Other Uncategorized Messages** and **Create Reply Draft**.
   - Google Sheets OAuth2 on **Add enquiry to sales sheet**.
   - OpenAI on **Classifier Model**, **Extractor Model** and **Enquiry Reply Model**.
4. In Gmail, create six labels, for example `Studio/New Enquiry`, `Studio/Existing Client`, `Studio/Job Application`, `Studio/Spam`, `Studio/Needs Review` and `Studio/Other`. A slash in a label name creates a nested label.
5. Replace the placeholders in the table below.
6. Create a Google Sheet. In row 1, type these headers exactly: `name`, `email`, `phone`, `project_type`, `budget_min`, `budget_max`, `location`, `timeline`, `received_at`, `subject`, `lead_status`, `follow_up_reason`. Select the phone column and set Format, Number, Plain text before any rows are written.
7. Send yourself a few test emails, ideally from a second account. Include a clear enquiry, a short enquiry with no budget, a current client update, a job application, an obvious spam message and one that fits none of those. Use the sample files in `sample-data/` as a guide.
8. Run the workflow with the Gmail Trigger's fetch test event, or by sending the emails after activating. Check that each email gets the right label and that the enquiry rows match the shape of `sample-data/output-example.json`.
9. Switch the workflow to Active.

## Placeholders to replace

| Placeholder | Node | Field | What to put |
|---|---|---|---|
| `YOUR_GMAIL_CREDENTIAL` | Gmail Trigger and the seven Gmail action nodes | Credential | Your Gmail OAuth2 credential |
| `YOUR_GOOGLE_SHEETS_CREDENTIAL` | Add enquiry to sales sheet | Credential | Your Google Sheets OAuth2 credential |
| `YOUR_OPENAI_CREDENTIAL` | Classifier Model, Extractor Model, Enquiry Reply Model | Credential | Your OpenAI API credential |
| `YOUR_GOOGLE_SHEET_ID` | Add enquiry to sales sheet | Document | Pick your sheet from the list, or paste the ID from its URL |
| `YOUR_GMAIL_LABEL_ID_NEW_ENQUIRY` | Label New Enquiry | Label Names or IDs | Pick your new enquiry label |
| `YOUR_GMAIL_LABEL_ID_EXISTING_CLIENT` | Label Existing Client | Label Names or IDs | Pick your existing client label |
| `YOUR_GMAIL_LABEL_ID_JOB_APPLICATION` | Label Job Applications | Label Names or IDs | Pick your job application label |
| `YOUR_GMAIL_LABEL_ID_SPAM` | Label Spam | Label Names or IDs | Pick your spam label |
| `YOUR_GMAIL_LABEL_ID_NEEDS_REVIEW` | Label Needs Review | Label Names or IDs | Pick your needs review label |
| `YOUR_GMAIL_LABEL_ID_OTHER` | Label Other Uncategorized Messages | Label Names or IDs | Pick your other label |
| `YOUR_STUDIO_NAME` | Draft Enquiry Reply | Prompt text | The name of your studio |

Gmail label IDs are specific to each Gmail account, so choose the labels from the dropdown after the Gmail credential is connected. Do not type the placeholder IDs.

The Google Sheets node is set to the sheet named `Sheet1`. If your tab has another name, change it in the node. Its cell format is set to "Let n8n format" (raw), and I recommend keeping it, because the default made Sheets read a leading `+` in a phone number as a formula.

## Troubleshooting

- **A Gmail node says the credential is missing or expired.** Open the credential and reconnect the Google account. OAuth tokens for apps in testing mode can expire, and reconnecting fixes it.
- **No label is applied.** Check that you ran the whole workflow, not only the classifier node, and that each Add Label node is connected to the matching output of Sort Email Type. Open one label node and check that Message ID shows a real value.
- **Everything lands in one branch.** Check that Simplify is off on the Gmail Trigger, so the full text body arrives. With Simplify on, the node returns only a short snippet and the classifier and extractor lose most of the email.
- **The sheet node says the sheet or tab cannot be found.** Reselect the document, and make sure the tab name in the node matches the tab in your sheet.
- **A phone number shows `#ERROR!`.** Format the phone column as plain text, delete the row, and make sure the cell format option on the Google Sheets node is "Let n8n format".
- **Budgets show as 0.** Check that the budget fields in Format Enquiry Row end in `?? null`, so a missing budget stays empty.
- **The OpenAI nodes fail.** Attach your own OpenAI credential and check that the model name is available on your account.
- **The Gmail draft has no recipient.** Open the Create Reply Draft node and set the To option to the sender's address, for example the `from.value[0].address` field of the original email.
