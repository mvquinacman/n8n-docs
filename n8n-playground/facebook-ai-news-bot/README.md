# Facebook AI News Bot

An automated n8n workflow that monitors AI-related subreddits, uses AI to generate engaging content, and automatically posts AI news articles to your Facebook page.

## Overview

This workflow creates a hands-free content curation and publishing system for your Facebook page. It continuously monitors multiple AI-focused subreddits, filters for quality content, uses GPT-4o-mini to create engaging posts, and publishes them automatically to Facebook.

**Perfect for:** Tech influencers, AI news pages, technology bloggers, and anyone who wants to maintain an active Facebook presence with minimal effort.

## Features

### 🤖 Automated Content Discovery
- Monitors 8 major AI subreddits
- Fetches hot/trending posts every 6 hours
- Filters for quality (50+ upvotes)
- Adapts to current AI trends automatically

### 🎨 AI-Powered Content Generation
- Uses GPT-4o-mini for intelligent content creation
- Generates engaging, shareable Facebook posts
- Includes relevant hashtags automatically
- Adds call-to-action for better engagement
- Maintains conversational, viral-worthy tone

### 📱 Facebook Publishing
- Automatically posts to your Facebook page
- Customizable posting schedule
- No manual intervention required
- Source attribution included

### 📊 Quality Control
- Only processes highly upvoted posts
- Filters out removed/deleted content
- Focuses on substantive discussions
- Avoids spam and low-quality content

## Workflow Architecture

```
Schedule Trigger (Every 6 hours)
    ↓
Configure Subreddits (8 AI subreddits)
    ↓
Loop Through Each Subreddit
    ↓
Fetch Hot Posts from Reddit API
    ↓
Split & Extract Post Data
    ↓
Filter Quality Posts (50+ upvotes)
    ↓
Aggregate Top Posts
    ↓
Build AI Prompt with Post Summaries
    ↓
AI Agent Generates Facebook Post
    ↓
Post to Facebook Page
```

## Monitored Subreddits

The workflow monitors these AI-focused communities:

1. **r/artificial** - General AI discussions
2. **r/MachineLearning** - ML research and applications
3. **r/OpenAI** - OpenAI products and updates
4. **r/ArtificialIntelligence** - AI news and developments
5. **r/ChatGPT** - ChatGPT discussions
6. **r/LocalLLaMA** - Local LLM developments
7. **r/StableDiffusion** - AI image generation
8. **r/singularity** - AGI and future AI

*You can easily customize this list in the "Configure AI Subreddits" node.*

## Prerequisites

Before setting up this workflow, you'll need:

1. **n8n instance** (self-hosted or cloud)
2. **Reddit account** (for API access)
3. **OpenAI API key** (for content generation)
4. **Facebook Page** (you must be an admin)
5. **Facebook Developer App** (for API access)

## Setup Instructions

### Step 1: Reddit API Setup

Reddit doesn't require OAuth for read-only access, but you need a User-Agent:

1. **Create User-Agent String**:
   - Format: `YourAppName/1.0 by YourUsername`
   - Example: `AINewsBot/1.0 by u/myusername`

2. **Add to n8n**:
   - Go to Credentials → Add Credential
   - Select "Header Auth"
   - Name: `Reddit API (User-Agent)`
   - Header Name: `User-Agent`
   - Header Value: `[Your User-Agent string]`

**Why User-Agent?** Reddit requires this to identify your application and ensure compliance with their API guidelines.

### Step 2: OpenAI API Setup

1. **Get API Key**:
   - Visit https://platform.openai.com/api-keys
   - Create new secret key
   - Copy and save it securely

2. **Add to n8n**:
   - Go to Credentials → Add Credential
   - Select "OpenAI API"
   - Paste your API key
   - Save as "OpenAI API"

**Cost**: Approximately $0.10-$0.50 per day depending on posting frequency.

### Step 3: Facebook API Setup

This is the most complex part. Follow carefully:

#### 3.1 Create Facebook App

1. Go to https://developers.facebook.com/
2. Click "My Apps" → "Create App"
3. Select "Business" type
4. Fill in app details:
   - **App Name**: AI News Bot
   - **Contact Email**: Your email
5. Click "Create App"

#### 3.2 Add Facebook Pages Product

1. In your app dashboard, click "Add Product"
2. Find "Facebook Login" and click "Set Up"
3. In Products → Facebook Login → Settings:
   - Enable "Client OAuth Login"
   - Add Valid OAuth Redirect URIs: `https://your-n8n-instance.com/`

#### 3.3 Get Page Access Token

1. Go to Tools → Graph API Explorer
2. Select your app from dropdown
3. Click "Generate Access Token"
4. Grant required permissions:
   - `pages_manage_posts`
   - `pages_read_engagement`
   - `pages_show_list`
5. Click "Generate Access Token"
6. **Important**: This is a short-lived token

#### 3.4 Get Long-Lived Page Access Token

**Option A: Using Graph API Explorer**

1. In Graph API Explorer, use this endpoint:
   ```
   GET /me/accounts
   ```
2. This returns your pages with long-lived tokens
3. Find your page and copy the `access_token`

**Option B: Using Token Exchange**

```bash
curl -X GET "https://graph.facebook.com/v18.0/oauth/access_token?
  grant_type=fb_exchange_token&
  client_id=YOUR_APP_ID&
  client_secret=YOUR_APP_SECRET&
  fb_exchange_token=YOUR_SHORT_LIVED_TOKEN"
```

#### 3.5 Get Your Facebook Page ID

1. Visit your Facebook Page
2. Click "About"
3. Scroll to "Page Transparency"
4. Find "Page ID" - copy this number

Or use Graph API:
```
GET /me/accounts
```
This returns all your pages with their IDs.

#### 3.6 Add Credentials to n8n

1. Go to Credentials → Add Credential
2. Select "Facebook Graph API"
3. Enter:
   - **Access Token**: [Your long-lived page access token]
4. Save as "Facebook Graph API"

### Step 4: Import and Configure Workflow

1. **Import Workflow**:
   - In n8n, go to Workflows → Import from File
   - Select `Facebook AI News Bot.json`

2. **Assign Credentials**:
   - Click on "Fetch Hot Posts from Reddit" node
   - Select your Reddit credentials
   - Click on "OpenAI GPT-4o-mini" node
   - Select your OpenAI credentials
   - Click on "Post to Facebook Page" node
   - Select your Facebook credentials

3. **Configure Facebook Page ID**:
   - Click on "Facebook Page Config" node
   - Replace `YOUR_FACEBOOK_PAGE_ID` with your actual Page ID

4. **Customize Settings** (Optional):
   - Adjust posting schedule in "Schedule Trigger" node
   - Modify subreddit list in "Configure AI Subreddits" node
   - Change minimum upvotes in "Filter Quality Posts" node
   - Adjust AI creativity in "OpenAI GPT-4o-mini" node (temperature)

5. **Activate Workflow**:
   - Click "Active" toggle in top-right
   - Workflow will run automatically on schedule

## Testing the Workflow

Before activating, test manually:

1. **Disable Schedule**:
   - Right-click "Schedule Trigger" node
   - Select "Disable"

2. **Add Manual Trigger**:
   - Add "Manual Trigger" node
   - Connect to "Configure AI Subreddits"

3. **Execute Manually**:
   - Click "Execute Workflow"
   - Check each node's output
   - Verify Facebook post was created

4. **Re-enable Schedule**:
   - Delete Manual Trigger
   - Enable Schedule Trigger
   - Activate workflow

## Customization Options

### Adjust Posting Frequency

In "Schedule Trigger - Every 6 Hours" node:
- **Every 3 hours**: More frequent posts
- **Every 12 hours**: Less frequent posts
- **Daily at specific time**: Use cron expression
  ```
  0 9 * * *  (Daily at 9 AM)
  ```

### Change Subreddits

In "Configure AI Subreddits" node:
```json
[
  "artificial",
  "MachineLearning",
  "OpenAI",
  // Add your own:
  "deeplearning",
  "computervision",
  "LangChain"
]
```

### Modify Content Quality Filter

In "Filter Quality Posts" node:
- Change minimum upvotes (default: 50)
- Add time filter (posts from last 24 hours)
- Filter by specific keywords

### Customize AI Writing Style

In "Build AI Prompt" node, modify the `system_prompt`:

**Professional Tone:**
```
You are a professional AI industry analyst creating LinkedIn-style updates...
```

**Casual/Fun Tone:**
```
You are a fun tech enthusiast who makes AI news accessible and entertaining...
```

**Technical Depth:**
```
You are a technical AI researcher explaining complex developments to other experts...
```

### Add Image Support

To include images in Facebook posts:

1. Add HTTP Request node after "Extract Post Data"
2. Download image from Reddit post's URL
3. Modify Facebook node to include image
4. Use `additionalFields.media` parameter

## Cost Estimation

### Per Day (4 executions at 6-hour intervals):

**Reddit API**: Free (read-only)

**OpenAI API**:
- Input tokens: ~1,500 tokens × 4 = 6,000 tokens
- Output tokens: ~400 tokens × 4 = 1,600 tokens
- Cost: ~$0.15/day

**Facebook API**: Free (page posting)

**Total**: ~$0.15/day or **~$4.50/month**

### To Reduce Costs:
- Post less frequently (12-hour intervals)
- Use GPT-3.5-turbo instead of GPT-4o-mini
- Reduce max tokens in AI node

## Troubleshooting

### Common Issues

**Issue 1: "User-Agent required" Error**
- **Cause**: Reddit User-Agent not configured
- **Solution**: Add User-Agent header in Reddit HTTP Request node

**Issue 2: Facebook API Returns 190 Error**
- **Cause**: Invalid or expired access token
- **Solution**: Generate new long-lived page access token

**Issue 3: No Posts Generated**
- **Cause**: No Reddit posts meet quality threshold
- **Solution**: Lower upvote requirement or increase subreddit list

**Issue 4: AI-Generated Content is Repetitive**
- **Cause**: Low temperature setting
- **Solution**: Increase temperature to 0.8-1.0 in OpenAI node

**Issue 5: Facebook Permissions Error**
- **Cause**: Missing required permissions
- **Solution**: Regenerate token with `pages_manage_posts` permission

### Debugging Steps

1. **Test Each Section Separately**:
   - Disconnect nodes after Reddit fetching
   - Execute and verify Reddit data
   - Reconnect and test AI generation
   - Finally test Facebook posting

2. **Check Credentials**:
   - Verify all three credentials are properly configured
   - Test each API independently

3. **Review Node Outputs**:
   - Click on each node after execution
   - Check "Output" tab for data
   - Look for error messages

4. **Enable Workflow Logging**:
   - Settings → Executions → Save executions
   - Review failed executions

## Best Practices

### Content Strategy

1. **Maintain Authenticity**:
   - Review generated posts periodically
   - Adjust AI prompts to match your voice
   - Add your own insights occasionally

2. **Engagement**:
   - Respond to comments on posts
   - Monitor what content performs best
   - Adjust subreddit selection accordingly

3. **Consistency**:
   - Keep posting schedule regular
   - Don't overpost (max 4-6 times/day)
   - Quality over quantity

### Facebook Page Optimization

1. **Page Settings**:
   - Complete all page information
   - Add relevant categories
   - Enable notifications for comments

2. **Compliance**:
   - Follow Facebook community standards
   - Don't post misleading information
   - Respect copyright (AI news is fair use)

3. **Analytics**:
   - Monitor post performance in Facebook Insights
   - Track which AI topics get most engagement
   - Adjust workflow accordingly

### Maintenance

1. **Weekly**:
   - Review generated posts
   - Check for duplicate content
   - Monitor API costs

2. **Monthly**:
   - Refresh Facebook access token (if needed)
   - Update subreddit list based on trends
   - Review and optimize AI prompts

3. **Quarterly**:
   - Analyze post performance
   - Adjust posting frequency
   - Consider expanding to other platforms

## Advanced Features

### Add Sentiment Analysis

Before posting, analyze sentiment:

1. Add Code node after "Extract Generated Post"
2. Use sentiment analysis library
3. Only post positive/neutral content

### Multi-Platform Publishing

Extend to other platforms:

1. **Twitter/X**: Add Twitter node after AI generation
2. **LinkedIn**: Add LinkedIn node for professional audience
3. **Instagram**: Convert to image + caption

### Content Moderation

Add human review before posting:

1. Replace Facebook node with Slack/Email notification
2. Include approval link
3. Use webhook to trigger posting after approval

### A/B Testing

Test different AI prompts:

1. Create multiple prompt variations
2. Randomly select one per execution
3. Track performance in Facebook Insights
4. Use best-performing prompts

## Security Considerations

### API Key Protection

1. **Never commit credentials** to version control
2. Use n8n's credential management
3. Rotate Facebook tokens every 60 days
4. Monitor OpenAI API usage for anomalies

### Access Control

1. Limit Facebook app permissions to minimum required
2. Use separate Facebook app for each workflow
3. Don't share page access tokens

### Content Safety

1. Review AI-generated content periodically
2. Add keyword filters for sensitive topics
3. Implement content moderation checks
4. Monitor for misinformation

## Rate Limits

### Reddit API
- **Limit**: 60 requests per minute (without OAuth)
- **Workflow Impact**: Minimal (8 requests per execution)
- **Best Practice**: Keep subreddit list under 20

### OpenAI API
- **Limit**: Varies by tier (typically 10,000 RPM)
- **Workflow Impact**: Minimal (1 request per execution)
- **Best Practice**: Monitor quota in dashboard

### Facebook Graph API
- **Limit**: 200 calls per hour per user
- **Workflow Impact**: Minimal (1 call per execution)
- **Best Practice**: Don't run more than once per hour

## Alternative Configurations

### Using Different AI Models

**Anthropic Claude**:
- Replace OpenAI node with HTTP Request
- Use Claude API endpoint
- Adjust prompt format

**Google Gemini**:
- Add Gemini node (if available)
- Or use HTTP Request to Gemini API
- Similar cost structure

**Local LLM**:
- Use LM Studio or Ollama
- HTTP Request to local endpoint
- Free but requires local resources

### Different Content Sources

**Hacker News**:
- Replace Reddit node with Hacker News API
- Filter by AI-related stories
- Similar workflow structure

**Twitter/X**:
- Monitor AI influencers' tweets
- Aggregate trending AI topics
- Generate summaries

**RSS Feeds**:
- Use RSS Feed node
- Monitor AI news sites
- Aggregate and summarize

## Examples

### Sample Generated Post

```
🤖 Big moves in AI today!

OpenAI just announced a major breakthrough in multimodal understanding.
According to discussions on r/MachineLearning, the new model can process
and understand context across text, images, and audio simultaneously.

This could revolutionize:
✨ Virtual assistants
✨ Content creation tools
✨ Accessibility technology

What applications are you most excited about?

Read more: [Reddit discussion link]

#AI #ArtificialIntelligence #MachineLearning #OpenAI #TechNews #Innovation
```

### Custom Prompt Example

For a more technical audience:

```javascript
system_prompt: "You are a senior ML engineer writing technical updates
for other AI practitioners. Focus on implementation details, model
architecture, and practical applications. Use technical terminology
and cite specific metrics when available."
```

## FAQs

**Q: Can I post to multiple Facebook pages?**
A: Yes! Duplicate the Facebook posting nodes and configure different Page IDs and credentials.

**Q: Will this work with Facebook personal profiles?**
A: No, Facebook API only allows posting to Pages, not personal profiles.

**Q: Can I schedule posts for specific times instead of immediate publishing?**
A: Yes, but requires additional setup with Facebook's scheduled publishing API.

**Q: How do I avoid posting duplicate content?**
A: Add a database node to track posted Reddit links and filter them out.

**Q: Can I review posts before they're published?**
A: Yes, replace automatic posting with a Slack/email notification and approval workflow.

**Q: What if Reddit API changes?**
A: The workflow uses Reddit's public JSON API which is very stable. Monitor n8n community for updates.

## Support and Resources

### Documentation
- n8n Documentation: https://docs.n8n.io
- Reddit API: https://www.reddit.com/dev/api/
- OpenAI API: https://platform.openai.com/docs
- Facebook Graph API: https://developers.facebook.com/docs/graph-api

### Community
- n8n Community: https://community.n8n.io
- n8n Discord: https://discord.gg/n8n

### Related Workflows
- Social Media Scheduler
- Content Curation Bot
- Multi-Platform Publisher

## License

This workflow is provided as-is for use with n8n. Ensure compliance with:
- Reddit API Terms of Service
- OpenAI Terms of Service
- Facebook Platform Terms
- Your local content regulations

## Version History

- **v1.0** (2025-01-13): Initial release
  - 8 AI subreddits monitored
  - GPT-4o-mini integration
  - Facebook Page posting
  - Quality filtering
  - 6-hour schedule

## Contributing

Improvements welcome! Consider:
- Adding more AI subreddits
- Implementing image support
- Creating variant for other platforms
- Adding analytics tracking
- Building approval workflow

---

**Created by**: Claude AI Assistant
**Date**: January 13, 2025
**Workflow Type**: Social Media Automation / AI Content Generation
**n8n Version**: Compatible with n8n v1.0+
