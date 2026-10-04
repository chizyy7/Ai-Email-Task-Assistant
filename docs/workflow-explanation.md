# AI Email Task Assistant Workflow Explanation

This document explains how the AI Email Task Assistant workflow functions.

## Overview

The workflow automates the process of scanning incoming emails, using AI to determine if they contain actionable tasks, and sending notifications for those tasks.

## Node-by-Node Breakdown

### 1. Gmail Trigger (`b51e8d58-6cd3-405d-8126-4fe960622405`)
- **Purpose**: Watches for new emails in your Gmail inbox
- **Configuration**: 
  - Polls every minute
  - Uses filter `-from:me` to exclude sent emails
  - Credentials: Gmail account (OAuth2)

### 2. Prepare Email Data (`dd54b7d4-dbe1-4145-8b22-b6f1305bb4ca`)
- **Purpose**: Extracts and prepares email data for AI processing
- **Fields Created**:
  - `emailSubject`: The email subject line
  - `emailSender`: The email sender
  - `emailBody`: The email body (plain text version)
  - `emailReceivedTime`: Timestamp when email was received

### 3. AI Task Classifier (`2a3df699-a75f-4401-ad0e-107036d7f4c8`)
- **Purpose**: Uses Google Gemini to analyze email and extract task information
- **Model**: `models/gemini-3-flash-preview`
- **Credentials**: Google Gemini(PaLM) Api account
- **Output**: JSON with task analysis results

### 4. Is Actionable? (`2675206b-3e37-4604-ab41-939a5fbf0d55`)
- **Purpose**: Conditional node that splits workflow based on AI classification
- **Logic**: 
  - If `is_actionable` is true → continues to Prepare Notification
  - If `is_actionable` is false → workflow ends (no further action)

### 5. Prepare Notification (`10b8dd1b-e177-4833-aacc-14f74a29f6a4`)
- **Purpose**: Formats the extracted task data into an HTML notification
- **Fields Created**:
  - All original fields from AI classifier
  - `notificationMessage`: HTML-formatted notification content

### 6. Send Notification (`789888e4-b832-4d7c-9bd0-337f820093df`)
- **Purpose**: Sends the formatted notification via email
- **Configuration**:
  - Recipient: Configure your email address in the Send Notification node
  - Subject: Uses the extracted subject from email
  - Message: Uses the formatted HTML notification
  - Credentials: Gmail account (OAuth2)

## Data Flow

```
Gmail Trigger → Prepare Email Data → AI Task Classifier → Is Actionable? 
                                   ↙                 ↘
                         [No Action - End]    Prepare Notification → Send Notification
```

## Security Notes

- The workflow references credential IDs but does not contain actual secrets
- Credentials must be configured separately in your n8n instance
- Never share actual OAuth tokens or API keys