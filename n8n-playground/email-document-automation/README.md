# Email and Document Automation Workflow

A comprehensive n8n workflow for automated email processing, AI-powered classification, document storage on NAS, invoice validation, and project tracking.

## Overview

This workflow automates the entire email and document management lifecycle:
- Monitors multiple IMAP email accounts
- Uses AI to intelligently classify emails (Invoice, Project Update, Document, General)
- Automatically stores emails and attachments on NAS with organized folder structure
- Validates invoices with semi-automated AI checking
- Updates project tracking spreadsheets
- Generates PDFs and sends templated email responses
- Provides comprehensive logging and error alerting

## Features

### 1. Multi-Account Email Processing
- **Multiple IMAP Accounts**: Connect up to 3 (or more) email accounts simultaneously
- **Real-time Processing**: Polls for new emails every minute
- **Attachment Support**: Handles emails with multiple attachments
- **Metadata Extraction**: Captures sender, subject, body, date, and message ID

### 2. AI-Powered Email Classification
- **Intelligent Categorization**: Uses OpenAI GPT to classify emails into:
  - **INVOICE**: Billing, payment requests, invoices
  - **PROJECT_UPDATE**: Project progress, status updates
  - **DOCUMENT**: Important documents and files
  - **GENERAL**: General correspondence
- **Metadata Extraction**: Automatically extracts:
  - Invoice numbers, amounts, vendor names, due dates
  - Project names and status
  - Document types
- **Confidence Scoring**: Provides classification confidence levels

### 3. NAS/SMB Document Storage
- **Automatic Folder Creation**: Creates organized folder hierarchies:
  - **Invoices**: `/Invoices/YYYY/MM/VendorName/`
  - **Projects**: `/Projects/ProjectName/Updates/YYYY/MM/`
  - **Documents**: `/Documents/DocumentType/YYYY/MM/`
  - **General**: `/General/YYYY/MM/`
- **Smart File Naming**: Generates descriptive filenames with dates and metadata
- **SMB Protocol**: Compatible with most NAS devices (Synology, QNAP, etc.)

### 4. Semi-Automated Invoice Checking
- **AI Validation**: Checks invoices for:
  - Valid invoice number format
  - Reasonable amounts
  - Valid due dates
  - Known vendor verification
  - Anomaly detection
- **Manual Review Flagging**: Flags suspicious invoices for human review
- **Email Alerts**: Sends notifications when invoices need manual review
- **Issue Tracking**: Logs validation issues and recommendations

### 5. Project Spreadsheet Updates
- **Google Sheets Integration**: Automatically updates project tracking sheets
- **Invoice Tracking**: Logs all invoices with status, amounts, and file paths
- **Project Updates**: Records project status changes and updates
- **Document Registry**: Maintains a registry of all stored documents
- **Error Logging**: Tracks workflow errors in a dedicated sheet

### 6. PDF Generation & Templated Emails
- **PDF Creation**: Generates PDFs from email content for archival
- **Professional Templates**: Uses HTML templates for consistent formatting
- **Automated Responses**: Sends confirmation emails for project updates
- **Review Alerts**: Notifies finance team about invoices requiring review

### 7. Comprehensive Logging & Alerts
- **Success Logging**: Logs successful processing to Slack
- **Error Handling**: Catches all workflow errors
- **Multi-Channel Alerts**: Sends error notifications via:
  - Slack webhooks
  - Email alerts
  - Google Sheets error log
- **Detailed Error Reports**: Includes timestamps, error messages, and stack traces

## Workflow Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    IMAP Email Triggers                          │
│  (Account 1, Account 2, Account 3)                             │
└─────────────────┬───────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────────┐
│              Merge & Extract Email Data                         │
└─────────────────┬───────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────────┐
│          AI Email Classification (OpenAI)                       │
│  Categories: INVOICE | PROJECT_UPDATE | DOCUMENT | GENERAL     │
└─────────────┬───────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  Route by Category                              │
└──┬──────────┬──────────┬──────────┬────────────────────────────┘
   │          │          │          │
   ▼          ▼          ▼          ▼
┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐
│Invoice│  │Project│ │Document│ │General│
└───┬───┘  └───┬──┘  └───┬───┘ └───┬──┘
    │          │          │          │
    │          │          │          │
    ▼          ▼          ▼          ▼
┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐
│AI     │  │Generate│ │Process │ │Log    │
│Validate│  │PDF    │ │Attach  │ │Email  │
└───┬───┘  └───┬───┘  └───┬───┘ └───┬───┘
    │          │          │          │
    ▼          ▼          ▼          ▼
┌───────────────────────────────────────┐
│      Upload to NAS/SMB                │
└───────────────┬───────────────────────┘
                │
                ▼
┌───────────────────────────────────────┐
│    Update Google Sheets               │
└───────────────┬───────────────────────┘
                │
                ▼
┌───────────────────────────────────────┐
│  Send Notifications/Confirmations     │
└───────────────────────────────────────┘
```

## Prerequisites

Before using this workflow, ensure you have:

1. **n8n Instance**: Self-hosted or cloud-hosted n8n
2. **Email Accounts**: IMAP/SMTP credentials for your email accounts
3. **NAS/SMB Storage**: Network-attached storage with SMB protocol enabled
4. **OpenAI API Key**: For AI classification and validation
5. **Google Account**: For Google Sheets integration
6. **Slack Webhook** (Optional): For notifications
7. **PDF Generation API** (Optional): For PDF creation (or use alternative methods)

## Required Credentials

Set up the following credentials in n8n:

1. **IMAP Account 1, 2, 3**
   - Host, Port, Username, Password
   - TLS/SSL settings

2. **SMTP Credentials**
   - Host, Port, Username, Password
   - For sending emails

3. **NAS SMB Credentials**
   - Host, Share name, Username, Password
   - Domain (if required)

4. **OpenAI API**
   - API Key from OpenAI

5. **Google Sheets OAuth2**
   - OAuth2 credentials for Google Sheets API

6. **Slack Webhook** (Optional)
   - Webhook URL for notifications

## Installation

### Quick Start

1. **Import Workflow**
   ```
   - Open n8n
   - Go to Workflows
   - Click "Import from File"
   - Select "Email and Document Automation Workflow.json"
   ```

2. **Configure Credentials**
   - Set up all required credentials (see above)
   - Link credentials to respective nodes

3. **Update Configuration**
   - Replace placeholder spreadsheet IDs with your own
   - Update Slack webhook URLs
   - Configure email addresses
   - Set up NAS connection details

4. **Test the Workflow**
   - Send test emails to your IMAP accounts
   - Verify classification and storage
   - Check spreadsheet updates

5. **Activate**
   - Once tested, activate the workflow

### Detailed Setup

See [SETUP.md](./SETUP.md) for step-by-step configuration instructions.

See [QUICKSTART.md](./QUICKSTART.md) for a quick 5-minute setup guide.

## Configuration

### Email Accounts

Add or remove IMAP email triggers as needed:
- Each trigger monitors one email account
- All triggers merge into a single processing pipeline
- Configure poll frequency (default: every minute)

### Classification Customization

Modify the AI classification prompt to:
- Add custom categories
- Change classification criteria
- Extract additional metadata
- Adjust confidence thresholds

### Folder Structure Logic

Customize folder naming in the JavaScript code nodes:
- `Process Invoice Data` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:259)
- `Process Project Update` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:501)
- `Process Document` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:682)

### Spreadsheet Columns

Modify Google Sheets nodes to add/remove columns:
- `Update Invoice Spreadsheet` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:394)
- `Update Project Spreadsheet` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:584)
- `Update Document Spreadsheet` (n8n-playground/email-document-automation/Email and Document Automation Workflow.json:729)

## Usage

### Processing Invoices

1. Invoice email arrives → AI classifies as "INVOICE"
2. AI extracts vendor, invoice number, amount, due date
3. AI validates invoice details and flags issues
4. Invoice PDF saved to NAS: `/Invoices/2025/01/VendorName/Invoice_VendorName_INV123_20250117.pdf`
5. Spreadsheet updated with invoice details and validation status
6. If issues found → email alert sent to finance team
7. Success notification sent to Slack

### Processing Project Updates

1. Project email arrives → AI classifies as "PROJECT_UPDATE"
2. AI extracts project name and status
3. Email content converted to PDF
4. PDF saved to NAS: `/Projects/ProjectName/Updates/2025/01/ProjectUpdate_ProjectName_20250117.pdf`
5. Spreadsheet updated with project status
6. Confirmation email sent to sender
7. Success notification sent to Slack

### Processing Documents

1. Document email arrives → AI classifies as "DOCUMENT"
2. AI determines document type
3. Each attachment extracted
4. Files saved to NAS: `/Documents/DocumentType/2025/01/DocumentType_20250117_filename.pdf`
5. Spreadsheet updated with document registry
6. Success notification sent to Slack

### Processing General Emails

1. General email arrives → AI classifies as "GENERAL"
2. Email logged in general email spreadsheet
3. Stored for reference (optional)

## Error Handling

The workflow includes comprehensive error handling:

1. **Error Trigger**: Catches all workflow errors
2. **Error Capture**: Extracts error details (message, node, timestamp)
3. **Error Logging**: Logs to Google Sheets error log
4. **Multi-Channel Alerts**: Sends alerts via Slack and Email
5. **Detailed Reports**: Includes full error context for debugging

## Customization

### Adding New Email Categories

1. Update AI classification prompt with new category
2. Add new output to "Route by Category" switch node
3. Create processing logic for new category
4. Add storage and spreadsheet update nodes

### Changing Folder Structure

Edit the JavaScript code in processing nodes to modify:
- Folder hierarchy
- File naming conventions
- Date formatting
- Metadata inclusion

### Adding More Email Accounts

1. Duplicate an IMAP trigger node
2. Configure with new credentials
3. Connect to "Merge Email Sources" node
4. Update merge node to handle additional input

### Custom Validation Rules

Modify the "AI Validate Invoice" prompt to:
- Add vendor-specific rules
- Check against purchase orders
- Validate tax calculations
- Compare historical pricing

## Troubleshooting

### Emails Not Being Processed

- Check IMAP credentials are correct
- Verify firewall allows IMAP connections
- Ensure email account has new emails
- Check n8n execution logs

### NAS Upload Failures

- Verify SMB credentials
- Check network connectivity to NAS
- Ensure share permissions are correct
- Test folder creation permissions

### Spreadsheet Update Errors

- Verify Google Sheets OAuth2 credentials
- Check spreadsheet IDs are correct
- Ensure sheet names match configuration
- Verify column headers exist

### AI Classification Issues

- Check OpenAI API key is valid
- Verify API quota/usage limits
- Review classification prompt clarity
- Check for API rate limiting

## Performance

### Optimization Tips

1. **Polling Frequency**: Adjust IMAP poll frequency based on email volume
2. **Batch Processing**: Process multiple emails in parallel
3. **Error Recovery**: Implement retry logic for failed operations
4. **Caching**: Cache frequently accessed data
5. **Logging Level**: Adjust logging verbosity for production

### Expected Performance

- **Email Processing**: ~5-10 seconds per email (with AI)
- **Document Upload**: Depends on file size and network speed
- **Spreadsheet Update**: ~1-2 seconds per row
- **PDF Generation**: ~2-5 seconds per document

## Security

### Best Practices

1. **Credentials**: Store all credentials securely in n8n
2. **NAS Access**: Use dedicated service account with minimal permissions
3. **API Keys**: Rotate API keys regularly
4. **Email Security**: Use app-specific passwords for email accounts
5. **Network**: Use VPN or secure network for NAS access
6. **Logging**: Avoid logging sensitive data (passwords, API keys)

## Support

For issues, questions, or contributions:
- Open an issue in the repository
- Check n8n community forums
- Review n8n documentation

## License

This workflow is provided as-is under the MIT License.

## Changelog

### Version 1.0.0 (2025-01-17)
- Initial release
- Multi-account IMAP support
- AI-powered email classification
- NAS/SMB document storage with automatic folder creation
- Semi-automated invoice validation
- Project tracking spreadsheet updates
- PDF generation and templated emails
- Comprehensive logging and alerting

## Credits

Developed for automated email and document management workflows using n8n, OpenAI, and NAS storage.

---

**Maintained by:** n8n Community Contributors
**Last Updated:** January 17, 2025
