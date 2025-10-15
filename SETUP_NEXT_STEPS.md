# LinkedIn Poster - Next Setup Steps

## ✅ Completed
- Database schema created (5 tables)
- Slack bot "Banto" configured
- `/linkedin-draft` workflow imported to n8n (ID: V0kMmwx9nhAZStW2)

## 🔧 Required Setup Steps

### 1. Configure Claude API Credentials in n8n

The workflow needs a Claude API key to generate posts.

**Steps:**
1. Go to https://n8n-app.mgaliquor.buzz
2. Navigate to **Settings > Credentials**
3. Click **Add Credential**
4. Select **HTTP Header Auth**
5. Configure:
   - **Name**: `Claude API`
   - **Credential Type**: HTTP Header Auth
   - **Header Name**: `x-api-key`
   - **Header Value**: `[Your Claude API Key from console.anthropic.com]`
6. Click **Save**

**Get Claude API Key:**
- Go to https://console.anthropic.com/settings/keys
- Create new key if needed
- Copy the key (starts with `sk-ant-`)

### 2. Add Writing Samples to Database

The workflow uses your existing LinkedIn posts to match your writing style.

**Option A: Manual SQL Insert**
```sql
INSERT INTO writing_samples (post_text, source, posted_date, tags) VALUES
('Your first LinkedIn post text here...', 'linkedin', '2024-10-01', ARRAY['AI', 'automation']),
('Your second LinkedIn post text here...', 'linkedin', '2024-09-28', ARRAY['cloud', 'AWS']);
-- Add 5-10 of your best posts
```

**Option B: Create Import Workflow**
Let me know if you want me to create an n8n workflow to import posts from LinkedIn.

### 3. Configure Slack Slash Command Webhook

**Steps:**
1. Go to https://api.slack.com/apps
2. Select your app "Banto"
3. Go to **Slash Commands** in sidebar
4. Find `/linkedin-draft` command
5. Set **Request URL** to:
   ```
   https://n8n-app.mgaliquor.buzz/webhook/linkedin-draft
   ```
6. Click **Save**

### 4. Activate the Workflow

**Via n8n Web UI:**
1. Go to https://n8n-app.mgaliquor.buzz
2. Open workflow "LinkedIn Draft - Manual Command"
3. Click the **Activate** toggle in top right
4. Verify status shows "Active"

**Via API (alternative):**
```bash
curl -k -X PATCH https://n8n-app.mgaliquor.buzz/api/v1/workflows/V0kMmwx9nhAZStW2 \
  -H "X-N8N-API-KEY: $N8N_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"active": true}'
```

### 5. Test the Workflow

**In Slack:**
```
/linkedin-draft AI security for small businesses
```

**Expected Flow:**
1. Bot responds: "⏳ Generating your LinkedIn post..."
2. Workflow fetches your writing samples from DB
3. Calls Claude API to generate post
4. Saves draft to `linkedin_posts` table
5. Sends formatted message to Slack with buttons:
   - ✅ Approve & Post
   - ✏️ Refine
   - 🔄 Regenerate
   - 🗑️ Discard

## 🚨 Troubleshooting

### Error: "No writing samples found"
- Check if `writing_samples` table has data
- Query: `SELECT COUNT(*) FROM writing_samples;`
- Add at least 3-5 sample posts

### Error: "Claude API credentials not found"
- Verify credential named exactly "Claude API" exists in n8n
- Check credential ID matches in workflow
- Test credential connection in n8n UI

### Error: "Slack webhook timeout"
- Check if workflow is activated
- Verify webhook URL in Slack command configuration
- Check n8n execution logs for errors

### Workflow doesn't execute
- Ensure workflow is **Active** (toggle in top right)
- Check webhook path matches Slack configuration
- Review execution history in n8n

## 📊 Workflow Architecture

```
Slack Command (/linkedin-draft [topic])
    ↓
Parse Command & Generate Post ID
    ↓
Send "⏳ Generating..." response
    ↓
Fetch Writing Samples from DB (last 5 posts)
    ↓
Prepare Claude API Request (with style prompt)
    ↓
Call Claude API → Generate Draft
    ↓
Extract Post & Calculate Engagement Score
    ↓
Save to Database (status: pending_review)
    ↓
Send to Slack with Interactive Buttons
```

## 🎯 What's Working

- ✅ Webhook endpoint configured
- ✅ Database connection established
- ✅ Post ID generation (LP-YYYY-MM-DD-manual-N)
- ✅ Writing style prompt with banned phrases
- ✅ Engagement scoring algorithm
- ✅ Interactive Slack message with buttons

## 🔜 What's Not Built Yet

- ⏳ Button handler workflow (approve/refine/regenerate/skip)
- ⏳ Daily auto-generation workflow
- ⏳ LinkedIn posting integration
- ⏳ Post scheduling

## 📝 Notes

- The workflow uses `response_url` from Slack to send the final message
- Claude API key needs `messages` API access
- Engagement score is calculated based on: hook, story, emoji, CTA
- Post IDs include timestamp to avoid collisions

---

**Ready to test?** Complete steps 1-4 above, then try `/linkedin-draft` in Slack!
