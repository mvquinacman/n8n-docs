# Email and Document Automation - Quick Start Guide

Get up and running with the Email and Document Automation workflow in 15 minutes!

## Prerequisites Checklist

Before you begin, make sure you have:

- [ ] n8n instance (self-hosted or cloud)
- [ ] Email account credentials (IMAP/SMTP)
- [ ] NAS/SMB storage access
- [ ] OpenAI API key
- [ ] Google account for Sheets
- [ ] Slack workspace (optional)

## 5-Minute Setup

### Step 1: Import the Workflow (1 minute)

1. Open your n8n instance
2. Navigate to **Workflows** → **Add Workflow** → **Import from File**
3. Select `Email and Document Automation Workflow.json`
4. Click **Import**

### Step 2: Configure Email Accounts (2 minutes)

1. Click on **"IMAP Email Trigger - Account 1"** node
2. Click **"Credentials for IMAP"**
3. Click **"Create New Credential"**
4. Fill in your email details:
   ```
   User: your-email@domain.com
   Password: your-password-or-app-password
   Host: imap.gmail.com (or your IMAP server)
   Port: 993
   SSL/TLS: ✓ Enabled
   ```
5. Click **"Save"**
6. Repeat for Account 2 and Account 3 (or disable unused accounts)

### Step 3: Set Up OpenAI (1 minute)

1. Click on **"AI Classify Email"** node
2. Click **"Credentials for OpenAI"**
3. Click **"Create New Credential"**
4. Enter your OpenAI API key
5. Click **"Save"**

### Step 4: Configure NAS Storage (2 minutes)

1. Click on **"Upload to NAS (SMB)"** node
2. Click **"Credentials for SMB"**
3. Click **"Create New Credential"**
4. Fill in your NAS details:
   ```
   Host: nas.local or 192.168.1.100
   Share: your-share-name
   Domain: WORKGROUP (or your domain)
   Username: nas-username
   Password: nas-password
   ```
5. Click **"Save"**
6. Repeat for other NAS upload nodes

### Step 5: Set Up Google Sheets (3 minutes)

#### Create Spreadsheets

1. Open Google Sheets
2. Create a new spreadsheet named **"Invoice Tracking"**
   - Add headers: `Date`, `Vendor`, `Invoice Number`, `Amount`, `Due Date`, `Status`, `File Path`, `Validation Issues`, `Needs Review`
3. Create another spreadsheet named **"Project Updates"**
   - Add headers: `Date`, `Project Name`, `Status`, `From`, `Subject`, `File Path`
4. Create another spreadsheet named **"Document Registry"**
   - Add headers: `Date`, `Document Type`, `From`, `Subject`, `File Name`, `File Path`
5. Create another spreadsheet named **"Email Log"**
   - Add headers: `Date`, `From`, `Subject`, `Category`, `File Path`
6. Create another spreadsheet named **"Error Log"**
   - Add headers: `Timestamp`, `Workflow`, `Error Message`, `Node`, `Details`

#### Copy Spreadsheet IDs

1. For each spreadsheet, copy the ID from the URL:
   ```
   https://docs.google.com/spreadsheets/d/SPREADSHEET_ID_HERE/edit
   ```

#### Configure Google Sheets Credentials

1. Click on **"Update Invoice Spreadsheet"** node
2. Click **"Credentials for Google Sheets"**
3. Click **"Create New Credential"**
4. Follow OAuth2 flow to authorize
5. Update the `documentId` with your Invoice Tracking spreadsheet ID
6. Repeat for other Google Sheets nodes:
   - Update Project Spreadsheet
   - Update Document Spreadsheet
   - Log General Email
   - Log Error to Spreadsheet

### Step 6: Configure Email Sending (2 minutes)

1. Click on **"Send Review Alert Email"** node
2. Click **"Credentials for SMTP"**
3. Click **"Create New Credential"**
4. Fill in SMTP details:
   ```
   User: your-email@domain.com
   Password: your-password-or-app-password
   Host: smtp.gmail.com (or your SMTP server)
   Port: 587 (or 465 for SSL)
   SSL/TLS: ✓ Enabled
   ```
5. Update the `fromEmail` and `toEmail` fields in the node parameters

### Step 7: Configure Slack Notifications (Optional, 2 minutes)

1. Go to your Slack workspace
2. Create an Incoming Webhook:
   - Visit https://api.slack.com/apps
   - Create New App → From Scratch
   - Enable Incoming Webhooks
   - Add New Webhook to Workspace
   - Copy the Webhook URL
3. Click on **"Log Success to Slack"** node
4. Update the `url` parameter with your webhook URL
5. Repeat for **"Alert Error to Slack"** node

### Step 8: Test the Workflow (2 minutes)

1. Click **"Execute Workflow"** button
2. Or manually trigger by:
   - Sending a test email to one of your IMAP accounts
   - Waiting for the next poll (1 minute)
3. Check the execution log for results
4. Verify:
   - Email was classified correctly
   - Files uploaded to NAS
   - Spreadsheet updated
   - Notifications sent

### Step 9: Activate the Workflow (30 seconds)

1. Toggle the **"Active"** switch at the top
2. Workflow will now run automatically!

## Quick Configuration Checklist

After setup, verify these settings:

### Email Triggers
- [ ] All IMAP credentials configured
- [ ] Poll frequency set (default: 1 minute)
- [ ] Test emails received and processed

### AI Classification
- [ ] OpenAI API key valid
- [ ] Classification prompt reviewed
- [ ] Test classifications accurate

### NAS Storage
- [ ] SMB credentials configured
- [ ] Network connectivity verified
- [ ] Folder creation permissions confirmed
- [ ] Test file upload successful

### Spreadsheets
- [ ] All spreadsheet IDs updated
- [ ] Sheet names match configuration
- [ ] Column headers exist
- [ ] OAuth2 credentials authorized
- [ ] Test row insertion successful

### Email Sending
- [ ] SMTP credentials configured
- [ ] From/To email addresses updated
- [ ] Test email sent successfully

### Notifications
- [ ] Slack webhooks configured (optional)
- [ ] Test notifications received

## Testing Scenarios

### Test 1: Invoice Email

1. Send an email with subject: "Invoice INV-2025-001 - $1,500"
2. Include text: "Please find attached invoice from Acme Corp. Amount: $1,500. Due: 2025-02-15"
3. Attach a PDF file
4. Verify:
   - Classified as INVOICE
   - Extracted vendor, amount, invoice number
   - AI validation completed
   - Uploaded to `/Invoices/2025/01/Acme_Corp/`
   - Spreadsheet updated
   - Alert sent if validation issues

### Test 2: Project Update Email

1. Send an email with subject: "Project Alpha - Status Update"
2. Include text: "Project Alpha is progressing well. Status: On Track"
3. Verify:
   - Classified as PROJECT_UPDATE
   - Extracted project name and status
   - PDF generated from email
   - Uploaded to `/Projects/Project_Alpha/Updates/2025/01/`
   - Spreadsheet updated
   - Confirmation email sent

### Test 3: Document Email

1. Send an email with subject: "Contract Documents"
2. Attach 2-3 files
3. Verify:
   - Classified as DOCUMENT
   - All attachments processed
   - Uploaded to `/Documents/Contract/2025/01/`
   - Spreadsheet updated with all files

### Test 4: General Email

1. Send a regular email
2. Verify:
   - Classified as GENERAL
   - Logged in email log spreadsheet

## Common Issues & Quick Fixes

### Issue: Email Not Processing
**Fix:** Check IMAP credentials and poll frequency

### Issue: AI Classification Failing
**Fix:** Verify OpenAI API key and check quota

### Issue: NAS Upload Error
**Fix:** Test SMB connection and folder permissions

### Issue: Spreadsheet Not Updating
**Fix:** Verify spreadsheet ID and sheet names

### Issue: No Notifications
**Fix:** Check Slack webhook URL or SMTP settings

## Next Steps

Now that your workflow is running:

1. **Monitor**: Check execution logs regularly
2. **Optimize**: Adjust classification prompts for better accuracy
3. **Customize**: Modify folder structure and naming conventions
4. **Scale**: Add more email accounts or categories
5. **Extend**: Add custom processing logic for specific use cases

## Advanced Configuration

For advanced customization, see:
- [README.md](./README.md) - Full documentation
- [SETUP.md](./SETUP.md) - Detailed setup guide

## Support

Need help?
- Check the [README.md](./README.md) for troubleshooting
- Review n8n documentation
- Open an issue in the repository

---

**Time to First Email Processed:** ~15 minutes
**Difficulty:** Intermediate
**Estimated Setup Time:** 15-30 minutes

Happy automating! 🚀
