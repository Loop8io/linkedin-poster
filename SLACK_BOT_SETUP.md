# Slack Bot Setup Guide - LinkedIn Poster Agent

Complete step-by-step guide to create and configure your Slack bot for the LinkedIn Poster Agent.

---

## 📋 Prerequisites

- [ ] Admin access to your Slack workspace
- [ ] n8n instance running at `n8n-app.mgaliquor.buzz`
- [ ] PostgreSQL database access

---

## 🚀 Part 1: Create Slack App

### Step 1: Go to Slack API Portal
1. Navigate to https://api.slack.com/apps
2. Click **"Create New App"**
3. Select **"From scratch"**

### Step 2: Configure Basic Information
- **App Name:** `LinkedIn Poster Agent`
- **Workspace:** Select your workspace (e.g., Loop8 workspace)
- Click **"Create App"**

---

## 🔧 Part 2: Configure Slash Commands

### Step 3: Add Slash Commands
In your app settings, go to **"Slash Commands"** in the left sidebar.

**NOTE:** The bot uses a **hybrid auto + manual workflow**. Daily posts auto-generate at 9am. You can also create posts on-demand.

#### Command 1: `/linkedin-draft` (Manual Post Creation)
Click **"Create New Command"** and fill in:

```
Command: /linkedin-draft
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-draft
Short Description: Generate a LinkedIn post draft with your topic
Usage Hint: [topic or idea]
```

**Example usage:**
```
/linkedin-draft AI governance for small businesses
/linkedin-draft multi-cloud cost optimization tips
```

#### Command 2: `/linkedin-approve` (Approve & Post)
```
Command: /linkedin-approve
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-approve
Short Description: Approve and post a draft to LinkedIn
Usage Hint: [post-id]
```

**Example usage:**
```
/linkedin-approve LP-2025-10-14-auto
/linkedin-approve LP-2025-10-14-manual-1
```

#### Command 3: `/linkedin-refine` (Refine Draft)
```
Command: /linkedin-refine
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-refine
Short Description: Request changes to a draft
Usage Hint: [post-id] [instructions]
```

**Example usage:**
```
/linkedin-refine LP-2025-10-14-auto make it shorter
/linkedin-refine LP-2025-10-14-manual-1 add a personal story
```

#### Command 4: `/linkedin-regenerate` (New Draft, Same Topic)
```
Command: /linkedin-regenerate
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-regenerate
Short Description: Generate a new draft on the same topic
Usage Hint: [post-id]
```

**Example usage:**
```
/linkedin-regenerate LP-2025-10-14-auto
```

#### Command 5: `/linkedin-schedule` (Schedule Post)
```
Command: /linkedin-schedule
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-schedule
Short Description: Schedule a post for later
Usage Hint: [post-id] [time]
```

**Example usage:**
```
/linkedin-schedule LP-2025-10-14-auto tomorrow 2pm
/linkedin-schedule LP-2025-10-14-manual-1 Friday 9am
```

#### Command 6: `/linkedin-skip` (Skip Post)
```
Command: /linkedin-skip
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-skip
Short Description: Skip this post idea
Usage Hint: [post-id]
```

**Example usage:**
```
/linkedin-skip LP-2025-10-14-auto
```

#### Command 7: `/linkedin-settings` (Configure Auto-Generation)
```
Command: /linkedin-settings
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-settings
Short Description: Configure daily auto-generation settings
Usage Hint: (interactive menu)
```

**Example usage:**
```
/linkedin-settings
```

#### Command 8: `/linkedin-pause` (Pause Auto-Generation)
```
Command: /linkedin-pause
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-pause
Short Description: Pause daily auto-generation
Usage Hint: (no parameters)
```

**Example usage:**
```
/linkedin-pause
```

#### Command 9: `/linkedin-resume` (Resume Auto-Generation)
```
Command: /linkedin-resume
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-resume
Short Description: Resume daily auto-generation
Usage Hint: (no parameters)
```

**Example usage:**
```
/linkedin-resume
```

#### Command 10: `/linkedin-help` (Help)
```
Command: /linkedin-help
Request URL: https://n8n-app.mgaliquor.buzz/webhook/linkedin-help
Short Description: Get help using the LinkedIn Poster
Usage Hint: (no parameters needed)
```

**Example usage:**
```
/linkedin-help
```

---

## 🔐 Part 3: Configure OAuth & Permissions

### Step 4: Set Bot Token Scopes
1. Go to **"OAuth & Permissions"** in the left sidebar
2. Scroll to **"Scopes"** section
3. Under **"Bot Token Scopes"**, add the following:

#### Required Scopes:
```
chat:write          - Send messages as the bot
commands            - Add slash commands
users:read          - View people in the workspace
channels:read       - View basic channel info
```

#### Optional (for future features):
```
files:write         - Upload files (for image posts)
reactions:write     - Add emoji reactions
chat:write.public   - Send messages to channels the bot isn't in
```

### Step 5: Install App to Workspace
1. Scroll to top of **"OAuth & Permissions"** page
2. Click **"Install to Workspace"**
3. Review permissions and click **"Allow"**
4. **IMPORTANT:** Copy the **Bot User OAuth Token** - it starts with `xoxb-`
5. Save this token securely - you'll need it for n8n

---

## 🔗 Part 4: Configure Interactive Components (for buttons)

### Step 6: Enable Interactivity
1. Go to **"Interactivity & Shortcuts"** in the left sidebar
2. Toggle **"Interactivity"** to **ON**
3. Set **Request URL:**
   ```
   https://n8n-app.mgaliquor.buzz/webhook/linkedin-interactions
   ```
4. Click **"Save Changes"**

This enables the interactive buttons like [Refine], [Post Now], [Discard] in bot responses.

---

## 🎨 Part 5: Customize App Appearance (Optional)

### Step 7: Add Bot Display Name & Icon
1. Go to **"Basic Information"**
2. Under **"Display Information"**, set:
   - **App Name:** LinkedIn Poster Agent
   - **Short Description:** Generate authentic LinkedIn posts in Ruby's voice
   - **App Icon:** Upload a custom icon (suggestion: Loop8 logo or LinkedIn icon)
   - **Background Color:** Choose brand color (e.g., Loop8 blue)

---

## 📝 Part 6: Get Credentials for n8n

### Step 8: Collect Required Tokens

You'll need these values for n8n configuration:

#### 1. **Bot User OAuth Token** (from Step 5)
- Starts with `xoxb-`
- Found in OAuth & Permissions after installing app

#### 2. **Signing Secret**
1. Go to **"Basic Information"**
2. Scroll to **"App Credentials"**
3. Copy the **Signing Secret** (used to verify requests from Slack)

#### 3. **App ID** (optional, for reference)
Found in **"Basic Information"** under **"App Credentials"**

---

## 🔧 Part 7: Configure n8n

### Step 9: Add Slack Credentials to n8n

1. Log into n8n at `https://n8n-app.mgaliquor.buzz`
2. Go to **Credentials** (in the left menu)
3. Click **"Add Credential"**
4. Search for and select **"Slack OAuth2 API"**
5. Fill in:
   - **Name:** `LinkedIn Poster Slack Bot`
   - **Bot User OAuth Token:** Paste the token from Step 5
   - **Signing Secret:** Paste from Step 8
6. Click **"Save"**

### Step 10: Create Webhook Endpoints in n8n

For each slash command, you need a corresponding webhook in n8n. We'll create these when building the workflows, but here are the URLs you already configured:

**Manual Post Creation:**
- `/linkedin-draft` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-draft`

**Review & Action Commands:**
- `/linkedin-approve` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-approve`
- `/linkedin-refine` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-refine`
- `/linkedin-regenerate` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-regenerate`
- `/linkedin-schedule` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-schedule`
- `/linkedin-skip` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-skip`

**Settings & Control:**
- `/linkedin-settings` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-settings`
- `/linkedin-pause` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-pause`
- `/linkedin-resume` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-resume`
- `/linkedin-help` → `https://n8n-app.mgaliquor.buzz/webhook/linkedin-help`

**Interactive Components (Buttons):**
- `https://n8n-app.mgaliquor.buzz/webhook/linkedin-interactions`

**Note:** Daily auto-generation runs on a schedule trigger (not a webhook)

---

## ✅ Part 8: Test the Bot

### Step 11: Verify Installation

1. Open your Slack workspace
2. In any channel (or DM to yourself), type:
   ```
   /linkedin-help
   ```
3. You should see the command autocomplete - if you do, the bot is installed! 🎉

**Note:** The command won't work yet because we haven't built the n8n workflows. That's next!

---

## 🔍 Troubleshooting

### Issue: Slash commands don't appear in autocomplete
**Solution:**
- Verify bot is installed to workspace
- Reinstall the app from OAuth & Permissions page
- Log out and back into Slack

### Issue: "dispatch_failed" error when using command
**Solution:**
- Check Request URLs in Slash Commands settings
- Ensure n8n webhooks are active (workflows must be turned ON)
- Verify SSL certificate on n8n instance is valid

### Issue: Bot doesn't respond
**Solution:**
- Check n8n workflow execution logs
- Verify Bot User OAuth Token is correct
- Ensure bot has `chat:write` permission

### Issue: "not_in_channel" error
**Solution:**
- Invite the bot to the channel: `/invite @LinkedIn Poster Agent`
- Or use the bot in a DM

---

## 📚 Important Slack API Concepts

### Slash Commands
- User types `/command [text]` in Slack
- Slack sends POST request to your Request URL
- Your server (n8n) responds within 3 seconds
- Can respond immediately or use `response_url` for delayed response

### Interactive Components (Buttons)
- User clicks a button in a bot message
- Slack sends POST request to Interactivity Request URL
- Payload includes which button was clicked and original message context
- Use this to handle [Refine], [Post Now], [Discard] buttons

### Message Formatting
Slack uses **mrkdwn** (similar to Markdown):
```
*bold*
_italic_
~strikethrough~
`code`
```

Blocks for rich formatting:
```json
{
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Your generated post appears here..."
      }
    },
    {
      "type": "actions",
      "elements": [
        {
          "type": "button",
          "text": {"type": "plain_text", "text": "Post Now 🚀"},
          "value": "post_now",
          "action_id": "post_now"
        }
      ]
    }
  ]
}
```

---

## 🔒 Security Best Practices

1. **Never commit tokens to git** - Store in n8n credentials only
2. **Verify signing secret** - Always validate requests are from Slack
3. **Use HTTPS only** - Slack requires secure webhooks
4. **Rotate tokens periodically** - Update in n8n when changed
5. **Limit bot permissions** - Only add scopes you actually use

---

## 📖 Reference Links

- [Slack API Documentation](https://api.slack.com/docs)
- [Slash Commands Guide](https://api.slack.com/interactivity/slash-commands)
- [Interactive Components](https://api.slack.com/interactivity/components)
- [Message Formatting](https://api.slack.com/reference/surfaces/formatting)
- [Block Kit Builder](https://app.slack.com/block-kit-builder/) - Visual message designer

---

## ✅ Setup Checklist

Before moving to n8n workflow development:

- [ ] Slack app created
- [ ] All 10 slash commands configured
  - [ ] /linkedin-draft
  - [ ] /linkedin-approve
  - [ ] /linkedin-refine
  - [ ] /linkedin-regenerate
  - [ ] /linkedin-schedule
  - [ ] /linkedin-skip
  - [ ] /linkedin-settings
  - [ ] /linkedin-pause
  - [ ] /linkedin-resume
  - [ ] /linkedin-help
- [ ] Bot token scopes added (chat:write, commands, users:read, channels:read)
- [ ] App installed to workspace
- [ ] Bot User OAuth Token saved securely
- [ ] Signing Secret saved securely
- [ ] Interactivity enabled with Request URL
- [ ] Credentials added to n8n
- [ ] `/linkedin-help` command appears in Slack autocomplete

---

## 🚀 Next Steps

Once this setup is complete:
1. ✅ Slack bot is configured
2. **Next:** Set up PostgreSQL database schema
3. **Then:** Build n8n workflows to handle the slash commands
4. **Finally:** Test end-to-end with real LinkedIn post generation!

---

**Setup Completed:** _[Date]_
**Configured By:** _[Your Name]_
**Workspace:** _[Workspace Name]_
