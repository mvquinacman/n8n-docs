# Quick Start Guide: Facebook AI News Bot

Get your automated AI news posting system running in 20 minutes!

## 🚀 Quick Setup (20 Minutes)

### Prerequisites Checklist

- [ ] n8n instance running
- [ ] Reddit account
- [ ] OpenAI API account
- [ ] Facebook Page (you must be admin)
- [ ] Facebook Developer account

---

## Step 1: Get Your API Keys (8 minutes)

### Reddit User-Agent (2 minutes)

Reddit doesn't need OAuth for reading, just a User-Agent:

1. Create your User-Agent string:
   ```
   Format: YourAppName/1.0 by YourUsername
   Example: AINewsBot/1.0 by u/johndoe
   ```

2. **Save this** - you'll need it in n8n

### OpenAI API Key (3 minutes)

1. Go to https://platform.openai.com/api-keys
2. Click "Create new secret key"
3. Copy the key (you won't see it again!)
4. **Cost**: ~$0.15/day

### Facebook Setup (3 minutes to get started)

**Quick Version** (Full version in main README):

1. Go to https://developers.facebook.com/
2. Create App → Business type
3. Add "Facebook Login" product
4. Go to Tools → Graph API Explorer
5. Generate token with permissions:
   - `pages_manage_posts`
   - `pages_show_list`

**Get Your Page ID**:
- Visit your Facebook Page
- Click "About" → "Page Transparency"
- Copy the "Page ID"

---

## Step 2: Setup n8n Credentials (5 minutes)

### Reddit Credentials

1. In n8n: Credentials → Add Credential
2. Select "Header Auth"
3. Configure:
   - **Name**: `Reddit API (User-Agent)`
   - **Header Name**: `User-Agent`
   - **Header Value**: `AINewsBot/1.0 by u/yourusername`
4. Save

### OpenAI Credentials

1. Credentials → Add Credential
2. Select "OpenAI API"
3. Paste your API key
4. Save as "OpenAI API"

### Facebook Credentials

1. Credentials → Add Credential
2. Select "Facebook Graph API"
3. Paste your access token
4. Save as "Facebook Graph API"

---

## Step 3: Import & Configure Workflow (5 minutes)

### Import

1. Download `Facebook AI News Bot.json`
2. In n8n: Workflows → Import from File
3. Select the JSON file

### Configure

**Assign Credentials** (Click each yellow node):

1. **"Fetch Hot Posts from Reddit"** node
   - Select: Reddit API (User-Agent)

2. **"OpenAI GPT-4o-mini"** node
   - Select: OpenAI API

3. **"Post to Facebook Page"** node
   - Select: Facebook Graph API

**Set Your Facebook Page ID**:

1. Click **"Facebook Page Config"** node
2. Change:
   ```
   page_id: "YOUR_FACEBOOK_PAGE_ID"
   ```
   to your actual Page ID (from Step 1)

---

## Step 4: Test Before Going Live (2 minutes)

### Manual Test

1. **Disable the Schedule**:
   - Right-click "Schedule Trigger" node
   - Click "Disable"

2. **Add Test Trigger**:
   - Add "Manual Trigger" node at the start
   - Connect to "Configure AI Subreddits"

3. **Run Test**:
   - Click "Execute Workflow"
   - Watch the execution flow
   - Check your Facebook Page for the post!

4. **If Successful**:
   - Delete Manual Trigger
   - Re-enable Schedule Trigger
   - Skip to Step 5

### Troubleshooting

**No Reddit Posts Retrieved?**
- Check User-Agent is correct
- Verify internet connection

**AI Generation Failed?**
- Check OpenAI API key
- Verify you have credits

**Facebook Posting Failed?**
- Verify access token has `pages_manage_posts` permission
- Check Page ID is correct
- Make sure you're a Page admin

---

## Step 5: Activate & Go Live (1 minute)

1. Remove test trigger (if you added one)
2. Enable Schedule Trigger
3. Click **"Active"** toggle (top-right)
4. Done! 🎉

Your bot will now:
- Run every 6 hours
- Fetch top AI news from Reddit
- Generate engaging Facebook posts
- Publish automatically

---

## 📋 First Execution Checklist

Within the first 6 hours, you should see:

- [ ] Workflow executed successfully
- [ ] Reddit posts fetched
- [ ] AI-generated post created
- [ ] Facebook post published
- [ ] No errors in execution log

---

## ⚙️ Quick Customization

### Change Posting Frequency

In "Schedule Trigger" node:

```
Every 3 hours:  interval = 3
Every 12 hours: interval = 12
Daily at 9 AM:  Use cron: 0 9 * * *
```

### Add/Remove Subreddits

In "Configure AI Subreddits" node:

```json
[
  "artificial",
  "MachineLearning",
  "OpenAI",
  "YourFavoriteSubreddit"
]
```

### Adjust Quality Filter

In "Filter Quality Posts" node:
- Default: 50+ upvotes
- More selective: 100+ upvotes
- Less selective: 20+ upvotes

---

## 💰 Cost Breakdown

### Daily (4 posts at 6-hour intervals):
- Reddit API: **Free**
- OpenAI: **~$0.15**
- Facebook: **Free**

**Total: ~$0.15/day or $4.50/month**

### To Reduce Costs:
- Post every 12 hours instead of 6
- Use GPT-3.5-turbo (50% cheaper)
- Reduce number of subreddits

---

## 🎯 What Happens Next?

### Automatic Operation

The workflow will:

1. **Every 6 hours**:
   - Check 8 AI subreddits
   - Find top 5 posts per subreddit
   - Filter for quality (50+ upvotes)

2. **AI Analysis**:
   - Aggregate best posts
   - Generate engaging Facebook content
   - Add hashtags and call-to-action

3. **Publish**:
   - Post to your Facebook page
   - Track source attribution
   - Log execution

### Monitor Your Bot

**Check Executions**:
- n8n → Executions tab
- View successful/failed runs
- Review generated posts

**Facebook Analytics**:
- Facebook Page → Insights
- Track post engagement
- See what content performs best

---

## 📱 Example Output

Your bot will generate posts like:

```
🤖 Breaking AI News!

The AI community is buzzing about OpenAI's latest multimodal
breakthrough. According to top discussions on r/MachineLearning,
the new architecture achieves unprecedented performance on
vision-language tasks.

Key highlights:
✨ 40% improvement over previous models
✨ Real-time processing capabilities
✨ Open research publication coming soon

What do you think this means for AI development?

Source: r/MachineLearning
Read the discussion: [link]

#AI #MachineLearning #OpenAI #TechNews #ArtificialIntelligence
```

---

## ❓ Common Questions

**Q: How do I know it's working?**
A: Check your Facebook Page - you should see posts appearing every 6 hours. Also check n8n Executions tab.

**Q: Can I review posts before publishing?**
A: Yes! See main README for approval workflow setup.

**Q: What if I don't want to post every 6 hours?**
A: Change the schedule in the Schedule Trigger node.

**Q: Can I use my personal Facebook instead of a Page?**
A: No, Facebook API only allows posting to Pages, not personal profiles.

**Q: Will this spam my audience?**
A: No - it posts every 6 hours (4x/day) with quality-filtered content. You can adjust frequency.

**Q: What if Reddit changes their API?**
A: The workflow uses Reddit's public JSON endpoints which are very stable. Updates will be announced in n8n community.

---

## 🆘 Quick Troubleshooting

### Workflow Not Executing

- Check if workflow is "Active" (toggle in top-right)
- Verify Schedule Trigger is enabled
- Check n8n has internet connection

### No Posts Generated

- Lower upvote threshold in Filter node
- Add more subreddits
- Check Reddit is accessible from your n8n instance

### Facebook API Errors

**Error 190**: Token expired or invalid
- Regenerate Facebook access token
- Update credentials in n8n

**Error 200**: Permission denied
- Token needs `pages_manage_posts` permission
- Regenerate with correct permissions

**Error 100**: Page ID invalid
- Double-check your Page ID
- Make sure you're using Page ID, not Page Name

---

## 🔧 Pro Tips

1. **Start Conservative**:
   - Use 12-hour interval initially
   - Monitor for 1 week
   - Adjust based on engagement

2. **Optimize for Your Audience**:
   - Track which AI topics get most likes
   - Adjust subreddit list accordingly
   - Customize AI prompt for your voice

3. **Maintain Authenticity**:
   - Review posts weekly
   - Engage with comments
   - Add your own posts occasionally

4. **Stay Compliant**:
   - Review Facebook community standards
   - Don't post misleading information
   - Respect copyright (news curation is fair use)

---

## 📚 Next Steps

Once your bot is running:

1. **Week 1**: Monitor and adjust
2. **Week 2**: Optimize posting times
3. **Week 3**: Fine-tune AI prompts
4. **Week 4**: Analyze performance and iterate

For advanced features, see the full README:
- Multi-platform posting
- Content moderation
- A/B testing
- Analytics integration

---

## ✅ Setup Complete!

You now have a fully automated AI news bot that will:
- ✅ Monitor 8 AI subreddits
- ✅ Generate engaging content
- ✅ Post to Facebook automatically
- ✅ Run 24/7 without intervention

**Welcome to hands-free social media management!** 🚀

---

**Need Help?**
- Full documentation: See `README.md`
- n8n Community: https://community.n8n.io
- Discord: https://discord.gg/n8n

**Ready to go?** Activate your workflow and watch the AI news roll in! 🎉
