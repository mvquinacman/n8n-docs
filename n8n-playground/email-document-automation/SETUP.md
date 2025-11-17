# Email and Document Automation - Detailed Setup Guide

This guide provides step-by-step instructions for setting up the Email and Document Automation workflow.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Environment Setup](#environment-setup)
3. [Credential Configuration](#credential-configuration)
4. [Spreadsheet Setup](#spreadsheet-setup)
5. [NAS Configuration](#nas-configuration)
6. [Workflow Configuration](#workflow-configuration)
7. [Testing](#testing)
8. [Deployment](#deployment)
9. [Monitoring](#monitoring)

## Prerequisites

### Required Services

1. **n8n Instance**
   - Version: 1.0.0 or higher
   - Self-hosted or n8n Cloud
   - Minimum 2GB RAM recommended
   - Stable internet connection

2. **Email Accounts**
   - IMAP-enabled email accounts
   - SMTP access for sending emails
   - App-specific passwords recommended (for Gmail, etc.)

3. **NAS/SMB Storage**
   - Network-attached storage with SMB/CIFS protocol
   - Dedicated folder structure for workflow
   - Write permissions for service account
   - Minimum 10GB storage recommended

4. **OpenAI Account**
   - OpenAI API key
   - Active billing account
   - Sufficient API credits

5. **Google Account**
   - Google Sheets access
   - Google Cloud project (for OAuth2)
   - Sheets API enabled

6. **Slack Workspace** (Optional)
   - Admin access to create webhooks
   - Dedicated channel for notifications

### Required Skills

- Basic n8n knowledge
- Email server configuration
- Network storage basics
- JSON understanding (for customization)

## Environment Setup

### 1. n8n Installation

If you don't have n8n installed:

#### Option A: Docker (Recommended)

```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

#### Option B: npm

```bash
npm install n8n -g
n8n start
```

#### Option C: n8n Cloud

Sign up at https://n8n.io/cloud

### 2. Access n8n

1. Open browser to `http://localhost:5678` (or your n8n URL)
2. Create admin account if first time
3. Log in to n8n interface

## Credential Configuration

### Email Accounts (IMAP)

For each email account you want to monitor:

#### Gmail Configuration

1. **Enable IMAP**
   - Go to Gmail Settings → Forwarding and POP/IMAP
   - Enable IMAP
   - Save changes

2. **Create App Password**
   - Go to Google Account → Security → 2-Step Verification
   - Scroll to "App passwords"
   - Generate password for "Mail"
   - Copy the 16-character password

3. **Configure in n8n**
   - Node: "IMAP Email Trigger - Account 1"
   - Credentials:
     ```
     User: your-email@gmail.com
     Password: [16-character app password]
     Host: imap.gmail.com
     Port: 993
     SSL/TLS: ✓ Enabled
     ```

#### Microsoft 365 / Outlook Configuration

1. **Enable IMAP**
   - Go to Outlook Settings → Mail → Sync email → POP and IMAP
   - Enable IMAP
   - Save

2. **Configure in n8n**
   - Credentials:
     ```
     User: your-email@outlook.com
     Password: [your password or app password]
     Host: outlook.office365.com
     Port: 993
     SSL/TLS: ✓ Enabled
     ```

#### Generic IMAP Server

1. **Get IMAP Settings**
   - Contact your email provider for IMAP settings
   - Typical settings:
     - Host: imap.domain.com
     - Port: 993 (SSL) or 143 (TLS)
     - SSL/TLS: Usually enabled

2. **Configure in n8n**
   - Use provided settings

### Email Sending (SMTP)

#### Gmail SMTP

1. **Use Same App Password**
   - From IMAP setup above

2. **Configure in n8n**
   - Node: "Send Review Alert Email"
   - Credentials:
     ```
     User: your-email@gmail.com
     Password: [16-character app password]
     Host: smtp.gmail.com
     Port: 587 (or 465 for SSL)
     SSL/TLS: ✓ Enabled
     ```

#### Microsoft 365 / Outlook SMTP

1. **Configure in n8n**
   - Credentials:
     ```
     User: your-email@outlook.com
     Password: [your password]
     Host: smtp.office365.com
     Port: 587
     SSL/TLS: ✓ Enabled
     ```

### OpenAI API

1. **Get API Key**
   - Go to https://platform.openai.com/api-keys
   - Create new secret key
   - Copy key (shown only once)

2. **Configure in n8n**
   - Node: "AI Classify Email"
   - Credentials:
     ```
     API Key: sk-...
     ```

3. **Check Usage Limits**
   - Go to https://platform.openai.com/account/billing
   - Set up billing if needed
   - Monitor usage

### Google Sheets OAuth2

1. **Create Google Cloud Project**
   - Go to https://console.cloud.google.com
   - Create new project: "n8n Email Automation"
   - Select project

2. **Enable Google Sheets API**
   - Go to "APIs & Services" → "Library"
   - Search "Google Sheets API"
   - Click "Enable"

3. **Create OAuth2 Credentials**
   - Go to "APIs & Services" → "Credentials"
   - Click "Create Credentials" → "OAuth client ID"
   - Application type: "Web application"
   - Name: "n8n Email Workflow"
   - Authorized redirect URIs:
     ```
     http://localhost:5678/rest/oauth2-credential/callback
     ```
     (or your n8n URL)
   - Click "Create"
   - Copy Client ID and Client Secret

4. **Configure in n8n**
   - Node: "Update Invoice Spreadsheet"
   - Credentials:
     ```
     Client ID: [from Google Cloud]
     Client Secret: [from Google Cloud]
     ```
   - Click "Connect my account"
   - Authorize access
   - Click "Save"

### NAS/SMB Storage

#### Synology NAS

1. **Create Shared Folder**
   - Control Panel → Shared Folder
   - Create folder: "EmailAutomation"
   - Set permissions

2. **Create User Account**
   - Control Panel → User
   - Create user: "n8n-automation"
   - Set password
   - Grant read/write access to "EmailAutomation" folder

3. **Enable SMB**
   - Control Panel → File Services → SMB
   - Enable SMB service
   - Note the workgroup name

4. **Configure in n8n**
   - Node: "Upload to NAS (SMB)"
   - Credentials:
     ```
     Host: nas.local (or IP: 192.168.1.100)
     Share: EmailAutomation
     Domain: WORKGROUP
     Username: n8n-automation
     Password: [user password]
     ```

#### QNAP NAS

1. **Create Shared Folder**
   - Control Panel → Privilege → Shared Folders
   - Create folder: "EmailAutomation"

2. **Create User**
   - Control Panel → Privilege → Users
   - Create user: "n8n-automation"
   - Grant access to shared folder

3. **Enable SMB**
   - Control Panel → Network & File Services → Microsoft Networking
   - Enable Microsoft Networking

4. **Configure in n8n**
   - Similar to Synology above

#### Windows SMB Share

1. **Create Shared Folder**
   - Create folder: `C:\EmailAutomation`
   - Right-click → Properties → Sharing
   - Click "Advanced Sharing"
   - Check "Share this folder"
   - Set permissions

2. **Configure in n8n**
   - Credentials:
     ```
     Host: computer-name or IP
     Share: EmailAutomation
     Domain: WORKGROUP (or domain name)
     Username: windows-username
     Password: windows-password
     ```

### Slack Webhook (Optional)

1. **Create Slack App**
   - Go to https://api.slack.com/apps
   - Click "Create New App"
   - Choose "From scratch"
   - Name: "Email Automation Alerts"
   - Select your workspace

2. **Enable Incoming Webhooks**
   - Click "Incoming Webhooks"
   - Toggle "Activate Incoming Webhooks" to ON
   - Click "Add New Webhook to Workspace"
   - Select channel (e.g., #email-automation)
   - Click "Allow"

3. **Copy Webhook URL**
   - Format: `https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXX`

4. **Configure in n8n**
   - Node: "Log Success to Slack"
   - Update `url` parameter with webhook URL
   - Repeat for "Alert Error to Slack"

## Spreadsheet Setup

### Create Google Sheets

1. **Invoice Tracking Spreadsheet**
   - Create new Google Sheet
   - Name: "Invoice Tracking"
   - Sheet name: "Invoices"
   - Headers (Row 1):
     | A | B | C | D | E | F | G | H | I |
     |---|---|---|---|---|---|---|---|---|
     | Date | Vendor | Invoice Number | Amount | Due Date | Status | File Path | Validation Issues | Needs Review |

2. **Project Updates Spreadsheet**
   - Create new Google Sheet
   - Name: "Project Updates"
   - Sheet name: "Project Updates"
   - Headers (Row 1):
     | A | B | C | D | E | F |
     |---|---|---|---|---|---|
     | Date | Project Name | Status | From | Subject | File Path |

3. **Document Registry Spreadsheet**
   - Create new Google Sheet
   - Name: "Document Registry"
   - Sheet name: "Documents"
   - Headers (Row 1):
     | A | B | C | D | E | F |
     |---|---|---|---|---|---|
     | Date | Document Type | From | Subject | File Name | File Path |

4. **Email Log Spreadsheet**
   - Create new Google Sheet
   - Name: "Email Log"
   - Sheet name: "Email Log"
   - Headers (Row 1):
     | A | B | C | D | E |
     |---|---|---|---|---|
     | Date | From | Subject | Category | File Path |

5. **Error Log Spreadsheet**
   - Create new Google Sheet
   - Name: "Error Log"
   - Sheet name: "Error Log"
   - Headers (Row 1):
     | A | B | C | D | E |
     |---|---|---|---|---|
     | Timestamp | Workflow | Error Message | Node | Details |

### Copy Spreadsheet IDs

For each spreadsheet:

1. Open spreadsheet in browser
2. Copy ID from URL:
   ```
   https://docs.google.com/spreadsheets/d/1abc...xyz/edit
                                            ^^^^^^^^
                                         This is the ID
   ```
3. Save ID for next step

### Update Workflow with Spreadsheet IDs

1. **Invoice Spreadsheet**
   - Node: "Update Invoice Spreadsheet"
   - Parameter: `documentId`
   - Value: `YOUR_INVOICE_SPREADSHEET_ID`

2. **Project Spreadsheet**
   - Node: "Update Project Spreadsheet"
   - Parameter: `documentId`
   - Value: `YOUR_PROJECT_SPREADSHEET_ID`

3. **Document Spreadsheet**
   - Node: "Update Document Spreadsheet"
   - Parameter: `documentId`
   - Value: `YOUR_DOCUMENT_SPREADSHEET_ID`

4. **Email Log Spreadsheet**
   - Node: "Log General Email"
   - Parameter: `documentId`
   - Value: `YOUR_GENERAL_EMAIL_LOG_SPREADSHEET_ID`

5. **Error Log Spreadsheet**
   - Node: "Log Error to Spreadsheet"
   - Parameter: `documentId`
   - Value: `YOUR_ERROR_LOG_SPREADSHEET_ID`

## NAS Configuration

### Folder Structure

Create the following folder structure on your NAS:

```
EmailAutomation/
├── Invoices/
├── Projects/
├── Documents/
└── General/
```

The workflow will automatically create subfolders as needed:

```
EmailAutomation/
├── Invoices/
│   ├── 2025/
│   │   ├── 01/
│   │   │   ├── VendorName1/
│   │   │   └── VendorName2/
│   │   └── 02/
│   └── 2024/
├── Projects/
│   ├── ProjectAlpha/
│   │   └── Updates/
│   │       └── 2025/
│   │           └── 01/
│   └── ProjectBeta/
├── Documents/
│   ├── Contract/
│   │   └── 2025/
│   │       └── 01/
│   └── Report/
└── General/
    └── 2025/
        └── 01/
```

### Permissions

Set permissions on the base folder:

- User: `n8n-automation`
- Permissions: Read, Write, Create, Delete
- Apply to: This folder, subfolders, and files

### Testing NAS Connection

1. **Test SMB Mount** (Linux/Mac)
   ```bash
   smbclient //nas.local/EmailAutomation -U n8n-automation
   ```

2. **Test File Upload**
   - Create test file: `test.txt`
   - Upload via SMB
   - Verify file appears on NAS

## Workflow Configuration

### Import Workflow

1. Download `Email and Document Automation Workflow.json`
2. In n8n: Workflows → Import from File
3. Select the JSON file
4. Click "Import"

### Configure Nodes

Go through each node and configure:

#### 1. Email Triggers (3 nodes)

- **IMAP Email Trigger - Account 1**
  - Credentials: [IMAP Account 1]
  - Mailbox: INBOX
  - Options:
    - Download attachments: ✓
    - Mark as read: ✓

- Repeat for Account 2 and Account 3
  - Or disable unused accounts

#### 2. AI Classification

- **AI Classify Email**
  - Credentials: [OpenAI API]
  - Model: gpt-4o-mini (or gpt-4)
  - Review system prompt for your needs

#### 3. NAS Upload Nodes

- **Upload to NAS (SMB)** (multiple nodes)
  - Credentials: [NAS SMB]
  - Verify path format

#### 4. Google Sheets Nodes

- **Update Invoice Spreadsheet**
  - Credentials: [Google Sheets OAuth2]
  - Document ID: [Invoice Spreadsheet ID]
  - Sheet Name: Invoices
  - Columns: Verify mapping

- Repeat for other Google Sheets nodes

#### 5. Email Sending Nodes

- **Send Review Alert Email**
  - Credentials: [SMTP]
  - From: `noreply@yourcompany.com`
  - To: `finance@yourcompany.com`
  - Update email addresses

- **Send Project Confirmation Email**
  - Update from/to addresses

- **Email Error Alert**
  - Update from/to addresses

#### 6. Slack Notification Nodes

- **Log Success to Slack**
  - URL: [Slack Webhook URL]

- **Alert Error to Slack**
  - URL: [Slack Webhook URL]

### Customize Prompts

Review and customize AI prompts:

1. **AI Classify Email** (Classification Prompt)
   - Add custom categories
   - Adjust classification criteria
   - Modify metadata extraction

2. **AI Validate Invoice** (Validation Prompt)
   - Add vendor-specific rules
   - Adjust validation criteria
   - Define what requires manual review

## Testing

### Pre-Flight Checklist

Before testing, verify:

- [ ] All credentials configured
- [ ] All spreadsheet IDs updated
- [ ] All email addresses updated
- [ ] NAS connection tested
- [ ] Slack webhooks configured (optional)

### Test Plan

#### Test 1: Invoice Processing

1. **Prepare Test Email**
   - To: [Your IMAP account]
   - Subject: "Invoice INV-2025-001"
   - Body: "Invoice from Acme Corp for $1,500. Due date: 2025-02-15"
   - Attach: Sample PDF

2. **Send Email**

3. **Wait for Processing** (1-2 minutes)

4. **Verify Results**
   - Check n8n execution log: Should show successful execution
   - Check NAS: File in `/Invoices/2025/01/Acme_Corp/`
   - Check Invoice Spreadsheet: New row added
   - Check Slack: Success notification
   - Check email: Review alert if validation issues

#### Test 2: Project Update Processing

1. **Prepare Test Email**
   - To: [Your IMAP account]
   - Subject: "Project Alpha - Status Update"
   - Body: "Project Alpha is on track. Status: In Progress"

2. **Send Email**

3. **Verify Results**
   - Check NAS: PDF in `/Projects/Project_Alpha/Updates/2025/01/`
   - Check Project Spreadsheet: New row added
   - Check email: Confirmation sent
   - Check Slack: Success notification

#### Test 3: Document Processing

1. **Prepare Test Email**
   - To: [Your IMAP account]
   - Subject: "Contract Documents"
   - Attach: 2-3 test files

2. **Send Email**

3. **Verify Results**
   - Check NAS: All files in `/Documents/Contract/2025/01/`
   - Check Document Spreadsheet: Row for each file
   - Check Slack: Success notification

#### Test 4: Error Handling

1. **Force an Error**
   - Temporarily disable NAS credentials
   - Send test email

2. **Verify Error Handling**
   - Check Error Log Spreadsheet: Error logged
   - Check Slack: Error alert sent
   - Check email: Error alert received

3. **Fix and Retry**
   - Re-enable NAS credentials
   - Manually re-execute workflow

### Troubleshooting Tests

If tests fail, check:

1. **Execution Log**
   - Click on failed execution
   - Review error messages
   - Check which node failed

2. **Common Issues**
   - IMAP: Check credentials and firewall
   - OpenAI: Verify API key and quota
   - NAS: Test SMB connection
   - Sheets: Verify IDs and sheet names
   - SMTP: Check credentials and port

## Deployment

### Production Checklist

Before activating for production:

- [ ] All tests passed
- [ ] Error handling verified
- [ ] Notification channels working
- [ ] Spreadsheets properly formatted
- [ ] NAS storage configured
- [ ] Email addresses updated to production
- [ ] Poll frequency set appropriately
- [ ] Resource usage acceptable

### Activate Workflow

1. Toggle "Active" switch in n8n
2. Workflow will start processing emails automatically

### Monitoring First 24 Hours

Monitor closely during first day:

- Check execution logs hourly
- Verify emails being processed correctly
- Monitor NAS storage usage
- Check spreadsheet updates
- Review classification accuracy

## Monitoring

### Daily Monitoring

- Review Error Log spreadsheet
- Check Slack notifications
- Verify storage usage
- Monitor API usage (OpenAI)

### Weekly Monitoring

- Review classification accuracy
- Check for missed emails
- Verify folder structure
- Review validation patterns
- Optimize prompts if needed

### Monthly Monitoring

- Review overall statistics
- Analyze common errors
- Optimize performance
- Update prompts and logic
- Clean up old logs

### Metrics to Track

1. **Processing Metrics**
   - Emails processed per day
   - Classification accuracy
   - Processing time per email
   - Error rate

2. **Storage Metrics**
   - NAS storage used
   - Files uploaded per day
   - Average file size

3. **API Usage**
   - OpenAI API calls per day
   - API cost per day
   - Average tokens per request

4. **Business Metrics**
   - Invoices processed
   - Invoices requiring manual review
   - Project updates tracked
   - Documents archived

## Maintenance

### Regular Maintenance Tasks

#### Weekly
- Review error logs
- Clean up old executions in n8n
- Check API usage and costs

#### Monthly
- Update AI prompts based on accuracy
- Review and optimize folder structure
- Archive old spreadsheet rows
- Update credentials if needed

#### Quarterly
- Review overall workflow performance
- Update documentation
- Train team on new features
- Plan improvements

## Support

For issues or questions:

- Check [README.md](./README.md) for troubleshooting
- Review n8n community forums
- Check n8n documentation
- Open GitHub issue

---

**Setup Time:** 30-60 minutes
**Difficulty:** Intermediate
**Maintenance:** Low

Good luck with your email automation! 🎉
