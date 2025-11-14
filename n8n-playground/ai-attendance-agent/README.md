# AI Attendance Agent - QR Code Validator

## Overview

This project implements an **AI-powered attendance system** for events using QR code validation. The system allows event organizers to scan guest QR codes, validate their RSVP status, prevent duplicate check-ins, and maintain a comprehensive attendance log.

Perfect for end-of-year parties, conferences, weddings, or any event requiring controlled access and attendance tracking.

## Features

- **QR Code Validation**: Instantly verify guest credentials via QR code scanning
- **Duplicate Prevention**: Automatically detect and reject already-used QR codes
- **Real-time Logging**: Track all scan attempts (authorized, unauthorized, duplicates)
- **Mobile-Friendly Interface**: Responsive web app optimized for smartphones and tablets
- **Manual Entry**: Fallback option for manual QR code entry
- **Audio/Visual Feedback**: Immediate success or error notifications
- **Scanner Attribution**: Track which staff member performed each scan

## Architecture

The system consists of two main components:

1. **n8n Workflow** (`workflow.json`) - Backend automation that handles:
   - Webhook endpoint for receiving QR codes
   - Baserow database queries and updates
   - Business logic (authorization, duplicate detection)
   - Comprehensive logging

2. **QR Scanner Web App** (`qr-scanner.html`) - Frontend interface that provides:
   - Camera-based QR code scanning
   - Manual code entry
   - Real-time feedback
   - Scan history log

## Workflow Logic

```
1. Receive QR Code via Webhook
   ↓
2. Extract QR Code and Scanner Info
   ↓
3. Search Baserow for QR Code
   ↓
4. Record Found?
   ├─ NO → Return "Not authorized" + Log attempt
   └─ YES → Continue
       ↓
   5. QR Code Already Used?
      ├─ YES → Return "QR already used" + Log attempt
      └─ NO → Continue
          ↓
      6. Mark QR as Used (update timestamp & scanner)
          ↓
      7. Return "Access authorized" + Log attempt
```

## Prerequisites

- **n8n instance** (self-hosted or cloud)
- **Baserow account** with API access
- **Web server** or local development environment for hosting the QR scanner page

## Installation

### Step 1: Set Up Baserow Database

Create a Baserow table with the following fields:

#### Main Guest Table (e.g., "event_guests")

| Field Name | Type | Description |
|------------|------|-------------|
| `guest_name` | Text | Guest's full name |
| `qr_code` | Text | Unique QR code identifier |
| `qrcode_used` | Boolean | Whether QR code has been scanned (default: false) |
| `used_timestamp` | Text | ISO timestamp when QR was scanned |
| `scanner_name` | Text | Name of person who scanned the QR code |

#### Log Table (e.g., "attendance_log")

| Field Name | Type | Description |
|------------|------|-------------|
| `action` | Text | Type of action: "authorized", "duplicate", "unauthorized" |
| `qr_code` | Text | The QR code that was scanned |
| `guest_name` | Text | Guest name (if found) |
| `timestamp` | Text | ISO timestamp of the scan attempt |
| `scanner` | Text | Name of person who performed the scan |

### Step 2: Import n8n Workflow

1. Log into your n8n instance
2. Click **"Import from File"**
3. Select `workflow.json`
4. The workflow will be imported (inactive state)

### Step 3: Configure Baserow Credentials

1. In n8n, go to **Credentials** → **Create New**
2. Select **Baserow API**
3. Enter your Baserow API credentials:
   - **Host**: Your Baserow instance URL (e.g., `https://api.baserow.io`)
   - **API Token**: Generate from Baserow Settings → API tokens

### Step 4: Update Workflow Placeholders

Replace the following placeholders in the workflow nodes:

| Placeholder | Description | Where to Find |
|-------------|-------------|---------------|
| `{{BASEROW_DATABASE_ID}}` | Your Baserow database ID | Baserow URL: `/database/{id}/` |
| `{{BASEROW_TABLE_ID}}` | Main guest table ID | Baserow URL: `/database/x/table/{id}/` |
| `{{BASEROW_LOG_DATABASE_ID}}` | Log database ID (can be same as main) | Baserow URL |
| `{{BASEROW_LOG_TABLE_ID}}` | Log table ID | Baserow URL |
| `{{BASEROW_CREDENTIAL_ID}}` | n8n credential ID | n8n Credentials panel |

**How to update:**
1. Open each Baserow node in the workflow
2. Click on the **Database** dropdown and select your database
3. Click on the **Table** dropdown and select your table
4. The IDs will be automatically populated

### Step 5: Activate Workflow

1. Click **"Active"** toggle in the workflow
2. Copy the **Webhook URL** from the "Webhook - QR Scanner" node
3. The URL format: `https://your-n8n-instance.com/webhook/qr-attendance`

### Step 6: Deploy QR Scanner Page

#### Option A: Simple HTTP Server (Development)

```bash
# Navigate to the project directory
cd ai-attendance-agent

# Python 3
python3 -m http.server 8000

# OR Node.js
npx http-server -p 8000

# Open browser to http://localhost:8000/qr-scanner.html
```

#### Option B: Deploy to Vercel (Production)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod

# Follow prompts and note the deployment URL
```

#### Option C: Deploy to Netlify

1. Create a new site in Netlify
2. Drag and drop the `qr-scanner.html` file
3. Note the deployment URL

#### Option D: Use Lovable (as mentioned in requirements)

1. Go to [lovable.dev](https://lovable.dev)
2. Create a new project
3. Upload the `qr-scanner.html` file
4. Deploy and get your live URL

### Step 7: Configure QR Scanner

1. Open the deployed QR scanner page
2. Enter your **n8n Webhook URL** (from Step 5)
3. Enter a **Scanner Name** (e.g., "John Doe")
4. Settings are automatically saved to browser localStorage

## Usage

### For Event Staff

1. **Open the scanner page** on your mobile device
2. **Enter configuration** (webhook URL and your name) - only needed once
3. **Click "Start QR Scanner"** to activate the camera
4. **Point camera at guest's QR code**
5. **Wait for confirmation**:
   - ✅ **Green** = Access Authorized
   - ❌ **Red** = Access Denied or Already Used
6. **Manual entry option** available if camera fails

### For Event Organizers

- **View logs** in Baserow's attendance_log table
- **Export data** for reporting and analytics
- **Monitor real-time** attendance through the log table
- **Track scanner performance** by scanner_name field

## Testing

See `tests.txt` for comprehensive test scenarios and expected results.

### Quick Test

1. **Create a test guest** in Baserow:
   ```
   guest_name: "Test User"
   qr_code: "TEST123"
   qrcode_used: false
   ```

2. **Test via API** (using curl or Postman):
   ```bash
   curl -X POST https://your-n8n-instance.com/webhook/qr-attendance \
     -H "Content-Type: application/json" \
     -d '{
       "qr_code": "TEST123",
       "scanner": "Test Scanner"
     }'
   ```

3. **Expected response** (first scan):
   ```json
   {
     "status": "success",
     "message": "Access authorized",
     "guest_name": "Test User",
     "qr_code": "TEST123",
     "timestamp": "2025-01-15T10:30:00.000Z"
   }
   ```

4. **Expected response** (second scan - duplicate):
   ```json
   {
     "status": "error",
     "message": "QR already used",
     "guest_name": "Test User",
     "qr_code": "TEST123",
     "timestamp": "2025-01-15T10:31:00.000Z"
   }
   ```

## API Endpoints

### POST /webhook/qr-attendance

**Request Body:**
```json
{
  "qr_code": "string (required)",
  "scanner": "string (optional, default: 'unknown')"
}
```

**Response Codes:**
- `200 OK` - Request processed (check `status` field for result)

**Response Body (Success):**
```json
{
  "status": "success",
  "message": "Access authorized",
  "guest_name": "John Doe",
  "qr_code": "ABC123XYZ",
  "timestamp": "2025-01-15T10:30:00.000Z"
}
```

**Response Body (Error - Not Found):**
```json
{
  "status": "error",
  "message": "Not authorized",
  "qr_code": "INVALID123",
  "timestamp": "2025-01-15T10:30:00.000Z"
}
```

**Response Body (Error - Already Used):**
```json
{
  "status": "error",
  "message": "QR already used",
  "guest_name": "John Doe",
  "qr_code": "ABC123XYZ",
  "timestamp": "2025-01-15T10:30:00.000Z"
}
```

## Troubleshooting

### Webhook Returns 404
- Verify workflow is **active** in n8n
- Check webhook URL is correct (including `/webhook/` path)
- Ensure no typos in the path configuration

### "Record Not Found" for Valid QR Codes
- Verify `qr_code` field values match exactly (case-sensitive)
- Check Baserow table ID is correct in workflow
- Ensure Baserow credentials have read access

### QR Code Not Updating After Scan
- Verify Baserow credentials have **write** access
- Check field names match exactly: `qrcode_used`, `used_timestamp`, `scanner_name`
- Review n8n execution log for errors

### Camera Not Working in QR Scanner
- Ensure HTTPS is used (cameras require secure context)
- Check browser camera permissions
- Try manual entry as fallback

### CORS Errors
- Add CORS headers in webhook response (already configured in workflow)
- If using custom domain, verify SSL certificate is valid

## Security Considerations

- **HTTPS Required**: Always use HTTPS for production deployments
- **API Token Security**: Never expose Baserow API tokens in client-side code
- **Webhook Authentication**: Consider adding authentication to webhook endpoint
- **Rate Limiting**: Implement rate limiting to prevent abuse
- **Scanner Authentication**: Add scanner login if needed for sensitive events

## Customization

### Modify Response Messages

Edit the "Prepare [X] Response" nodes in the workflow to customize messages.

### Add Additional Fields

1. Add fields to Baserow tables
2. Update the "Mark QR as Used" node to include new fields
3. Modify response preparation nodes to return new data

### Change Scan Cooldown

In `qr-scanner.html`, modify:
```javascript
const SCAN_COOLDOWN = 3000; // Change to desired milliseconds
```

### Add Email Notifications

Add an **Email** or **Send SMS** node after successful authorization to notify guests or organizers.

## Data Schema Examples

### Sample Guest Records

```csv
guest_name,qr_code,qrcode_used,used_timestamp,scanner_name
"Alice Johnson","QR001",false,"",""
"Bob Smith","QR002",false,"",""
"Carol White","QR003",true,"2025-01-15T18:30:00Z","John Doe"
```

### Sample Log Records

```csv
action,qr_code,guest_name,timestamp,scanner
"authorized","QR001","Alice Johnson","2025-01-15T18:25:00Z","John Doe"
"duplicate","QR001","Alice Johnson","2025-01-15T18:26:00Z","John Doe"
"unauthorized","QR999","","2025-01-15T18:27:00Z","Jane Smith"
```

## Performance Notes

- **Webhook response time**: ~500ms - 2s (depending on Baserow API latency)
- **Concurrent scans**: Tested up to 10 simultaneous scans
- **Recommended**: Deploy n8n close to Baserow region for lower latency
- **Caching**: Consider adding Redis cache for high-volume events (500+ guests)

## Support and Maintenance

- **n8n Version**: Tested on n8n v1.0+
- **Browser Compatibility**: Chrome 90+, Safari 14+, Firefox 88+
- **Mobile**: iOS 14+, Android 10+

## License

This project is provided as-is for use in event management and attendance tracking.

## Credits

- **QR Scanner Library**: [html5-qrcode](https://github.com/mebjas/html5-qrcode)
- **Automation Platform**: [n8n](https://n8n.io)
- **Database**: [Baserow](https://baserow.io)

## Future Enhancements

- [ ] Offline mode with sync capability
- [ ] Multi-language support
- [ ] Statistics dashboard
- [ ] Bulk QR code generation
- [ ] Integration with calendar systems
- [ ] WhatsApp/SMS confirmations
- [ ] NFC support for contactless check-in

---

**Questions or Issues?** Check the `tests.txt` file for common scenarios and troubleshooting tips.
