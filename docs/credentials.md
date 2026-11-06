# Credentials

The workflow uses three credential types. None are included in this repository, and the exported file only contains placeholder names.

## Gmail OAuth2 (`gmailOAuth2`)

Used by: Gmail Trigger, Label New Enquiry, Label Existing Client, Label Job Applications, Label Spam, Label Needs Review, Label Other Uncategorized Messages, Create Reply Draft.

What it is: a connection to the Gmail account that receives the studio's mail.

What it needs permission to do: read incoming messages, add labels to messages, and create drafts. It does not send email. In n8n, the Gmail OAuth2 credential asks for these permissions when you connect the account.

Where to create it: in n8n, open Credentials, create a new Gmail OAuth2 API credential, and follow the n8n instructions for creating a Google Cloud OAuth client (enable the Gmail API, create an OAuth client ID, add the n8n redirect URL, then sign in). On n8n Cloud there is a one-click Sign in with Google option for some credential types.

## Google Sheets OAuth2 (`googleSheetsOAuth2Api`)

Used by: Add enquiry to sales sheet.

What it is: a connection to the Google account that owns the enquiry sheet.

What it needs permission to do: read the list of spreadsheets and append rows to the chosen one.

Where to create it: in n8n, create a Google Sheets OAuth2 API credential. If you use your own Google Cloud OAuth client, enable the Google Sheets API and the Google Drive API on it.

## OpenAI API (`openAiApi`)

Used by: Classifier Model, Extractor Model, Enquiry Reply Model.

What it is: an API key for the model that sorts, extracts and drafts replies. The export uses `gpt-5-mini`.

Where to create it: create an API key in your OpenAI account, then add it in n8n as an OpenAI API credential. The key is billed to your OpenAI account, and each email costs one classifier call, plus an extractor call and a reply call for enquiries.

## Keeping secrets out of the repository

Never commit exported credentials, API keys or a `.env` file. The `.gitignore` in this repository excludes `.env`, `credentials*.json` and raw exports. If you re-export the workflow from n8n, run it through the same cleanup before committing: remove pinned data, remove credential IDs, and replace sheet and label IDs with placeholders.
