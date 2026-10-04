# AI Email Task Assistant

An intelligent email automation system that uses AI to classify incoming emails and extract actionable tasks, built with n8n and Google Gemini.

## Features

- **Automatic Email Processing**: Monitors your Gmail inbox for new emails (excluding sent items)
- **AI-Powered Classification**: Uses Google Gemini to analyze emails and determine if they contain actionable tasks
- **Task Extraction**: Extracts task details, deadlines, priority levels, and summaries from emails
- **Smart Notifications**: Sends formatted email notifications for actionable items
- **Conditional Logic**: Only processes emails that require action, reducing noise
- **Secure Integration**: Uses OAuth2 for Gmail and API key authentication for Gemini

## Setup Instructions

### Prerequisites

1. [n8n](https://n8n.io/) installed (either self-hosted or via n8n.cloud)
2. Google Cloud Project with:
   - Gmail API enabled
   - Gemini API access
   - OAuth2 credentials for Gmail
   - API key for Gemini/PaLM

### Configuration Steps

1. **Gmail Credentials**:
   - In n8n, create a new Google OAuth2 credential
   - Name it "Gmail account" (or update the workflow to match your credential name)
   - Configure with appropriate Gmail scopes (https://mail.google.com/)

2. **Gemini Credentials**:
   - In n8n, create new Google Gemini(PALM) API credential
   - Name it "Google Gemini(PaLM) Api account" (or update the workflow)
   - Add your Google API key with Gemini access

3. **Import Workflow**:
   - Copy the JSON from `Workflows/AI Email Task Assistant.json`
   - In n8n workflow editor, click the import button (top-right)
   - Paste the JSON and click import
   - After importing the workflow, configure your own Gmail and Google Gemini credentials in n8n

4. **Activate Workflow**:
   - Toggle the workflow to active status
   - The workflow will check for new emails every minute

## n8n Workflow Import Guide

The workflow consists of the following nodes:

1. **Gmail Trigger** - Watches for new emails (excluding sent items)
2. **Prepare Email Data** - Extracts subject, sender, body, and timestamp
3. **AI Task Classifier** - Uses Google Gemini to analyze email content
4. **Is Actionable?** - Conditional node that splits flow based on AI classification
5. **Prepare Notification** - Formats task details into HTML notification
6. **Send Notification** - Sends formatted email to specified recipient

### AI Classification Prompt

The workflow uses this prompt with Google Gemini:
```
You are an AI assistant that analyzes emails to determine if they contain actionable tasks.

Given the email details below, output a JSON object with the following structure:

{
  "is_actionable": boolean,
  "task": string,
  "deadline": string | null,
  "priority": "High" | "Medium" | "Low",
  "sender": string,
  "subject": string,
  "summary": string
}

// ... (full prompt in workflow)
```

## Security Considerations

- **Never commit credential files**: The workflow contains credential ID references but no actual secrets
- **Environment variables**: Consider using n8n's environment variables for API keys in production
- **Principle of least privilege**: Grant only necessary Gmail scopes (read-only access may suffice)
- **Regular credential rotation**: Rotate OAuth tokens and API keys periodically
- **Audit logs**: Monitor n8n execution logs for unusual activity

The workflow JSON in this repository contains only credential ID references, which are safe to share as they reference credentials stored securely in your n8n instance.

## Usage

Once activated, the workflow will:
1. Check for new emails in your Gmail inbox every minute
2. Skip emails you've sent (`-from:me` filter)
3. Pass email data to Google Gemini for analysis
4. If an actionable task is detected, send a formatted notification email
5. Non-actionable emails are ignored

Notification emails are sent to the configured recipient (update this address in the "Send Notification" node)

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

Distributed under the MIT License. See `LICENSE` file for details.

Project Link: https://github.com/chizyy7/Ai-Email-Task-Assistant