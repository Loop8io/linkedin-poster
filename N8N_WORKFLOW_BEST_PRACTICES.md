# n8n Workflow Best Practices

Learnings from building the LinkedIn Poster workflows (2025-10-16)

## 🔧 PostgreSQL Node - Data Flow

### Issue
PostgreSQL Insert operation only passes **inserted columns** to the next node. Any extra fields in `$json` are lost.

### Solution
Add an intermediate **Code node** after database operations to reconstruct the full data payload:

```javascript
// Node: "Prepare Slack Message" (after database insert)
const postData = $('Extract Generated Post').item.json;

return {
  json: {
    post_id: postData.post_id,
    draft_text_original: postData.draft_text_original,
    engagement_score: postData.engagement_score,
    channel_id: postData.channel_id
  }
};
```

### Best Practice
**Always use PostgreSQL Insert operation** (not `executeQuery`) for:
- Automatic SQL escaping (prevents injection)
- Type safety
- Cleaner code

**When you need to pass extra data:**
1. Database Insert node
2. Code node to reconstruct payload
3. Next operation (Slack, HTTP, etc.)

---

## 🔒 SQL Escaping

### ❌ Wrong - String Interpolation
```sql
INSERT INTO posts (text) VALUES ('{{ $json.text }}');
-- Fails with: "Don't worry" → SQL syntax error
```

### ✅ Right - Column Mapping
Use PostgreSQL node **Insert** operation with column mapping:
- Click expression icon (fx) for each field
- Enter: `{{ $json.field_name }}`
- Auto-escapes quotes, handles special characters

### Alternative - Manual Escaping (if using executeQuery)
```javascript
// In Code node before database
const escapedText = text.replace(/'/g, "''");
```

---

## 💬 Slack Integration

### Issue: `channel_not_found` Error
**Causes:**
1. Bot not invited to channel
2. Wrong channel ID format
3. Missing `chat:write` scope

### Solutions

**Option 1: Invite Bot to Channel**
```
/invite @BotName
```

**Option 2: Use Channel Name**
```json
{
  "channel": "#general"
}
```

**Option 3: Check Bot Scopes**
Go to Slack API → OAuth & Permissions → Scopes:
- `chat:write` - Post messages
- `chat:write.public` - Post to public channels without joining

### Delayed Responses
Slack slash command `response_url` expires in **30 minutes**.

For workflows longer than 30 min:
1. Use "Respond to Webhook" node immediately (shows "Processing...")
2. Use `chat.postMessage` API for final result
3. Need `channel_id` from original command

---

## 🔗 Data Access Patterns

### Access Current Node Data
```javascript
$json.field_name
```

### Access Previous Node Data
```javascript
$('Node Name').item.json.field_name
```

### When to Use Each
- **Use `$json`:** Current node's output
- **Use `$('NodeName')`:** When data is 2+ nodes back or lost through operations

### Common Issue: "Paired item data unavailable"
**Cause:** Trying to access node data that's too far back in chain

**Fix:**
```javascript
// Instead of: $('Parse Command').item.json.channel_id
// Pass it through intermediate nodes:

// In "Extract Post" node:
return {
  json: {
    ...otherFields,
    channel_id: $('Parse Command').item.json.channel_id
  }
};

// Then in later nodes:
$json.channel_id  // ✅ Works
```

---

## 🔐 SSL Certificates

### Let's Encrypt on Amazon Linux 2023

**Renew Certificate:**
```bash
sudo certbot renew
sudo systemctl reload nginx
```

**Auto-Renewal (Cron):**
```bash
# Install cron
sudo dnf install cronie -y
sudo systemctl enable crond
sudo systemctl start crond

# Add renewal job
sudo crontab -e

# Add this line:
0 0,12 * * * certbot renew --quiet --post-hook 'systemctl reload nginx'
```

**Verify Certificate:**
```bash
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com 2>/dev/null | openssl x509 -noout -dates
```

### Why This Matters
Slack webhooks **require valid SSL certificates**. Self-signed or expired certs will fail with `ssl_cacert` error.

---

## 🤖 Claude API Integration

### Credential Setup (n8n)
1. Settings → Credentials → Add Credential
2. Select: **HTTP Header Auth**
3. Configure:
   - **Name**: `Claude API`
   - **Header Name**: `x-api-key`
   - **Header Value**: `sk-ant-...` (your API key)

### HTTP Request Node Config
```javascript
{
  "method": "POST",
  "url": "https://api.anthropic.com/v1/messages",
  "headers": {
    "anthropic-version": "2023-06-01",
    "content-type": "application/json"
  },
  "body": {
    "model": "claude-3-5-sonnet-20241022",
    "max_tokens": 1024,
    "messages": [
      {
        "role": "user",
        "content": "Your prompt here"
      }
    ]
  }
}
```

---

## 📋 Workflow Design Patterns

### Pattern 1: Command → Process → Respond
```
Webhook Trigger
  ↓
Parse Input (Code)
  ↓
Immediate Response (Respond to Webhook)
  ↓
Long Process (API call, DB, etc.)
  ↓
Final Response (chat.postMessage)
```

### Pattern 2: Database → Reconstruct → Use
```
PostgreSQL Insert
  ↓
Reconstruct Data (Code node)
  ↓
Send to Slack / API
```

### Pattern 3: Multi-Source Merge
```
Node A → Code Node (merge)
Node B → Code Node (merge)
  ↓
Process merged data
```

**Merge example:**
```javascript
const dataA = $('Node A').item.json;
const dataB = $('Node B').item.json;

return {
  json: {
    ...dataA,
    ...dataB
  }
};
```

---

## 🧪 Testing Workflow

### Test Each Node Individually
1. Build workflow incrementally
2. Test after each node addition
3. Check data in execution view

### Common Test Points
- [ ] Webhook receives correct payload
- [ ] Data parsing extracts all fields
- [ ] API calls return expected structure
- [ ] Database inserts succeed
- [ ] Data flows through to final node
- [ ] Error handling works

### Debug Data Flow
Add temporary Code nodes that return:
```javascript
return {
  json: {
    debug: 'checkpoint',
    data: $json,
    previous_node: $('Previous Node').item.json
  }
};
```

---

## ⚠️ Common Pitfalls

### 1. Forgetting to Escape SQL
**Symptom:** `Syntax error at line 1 near "s"` or `"t"`
**Fix:** Use Insert operation, not executeQuery

### 2. Losing Data Through Nodes
**Symptom:** Field undefined in later node
**Fix:** Pass all needed fields through intermediate nodes

### 3. Expired Slack response_url
**Symptom:** Workflow succeeds but no Slack message
**Fix:** Use chat.postMessage for delayed responses

### 4. Wrong Expression Syntax
**Symptom:** Literal `{{ $json.field }}` appears in output
**Fix:** Enable expression mode (click fx icon)

### 5. SSL Certificate Expired
**Symptom:** Slack shows `ssl_cacert` error
**Fix:** Renew certificate, set up auto-renewal

---

## 📊 Workflow Performance

### Optimization Tips
1. **Minimize API calls** - Cache when possible
2. **Use batching** - Process multiple items together
3. **Parallel execution** - Split independent operations
4. **Database indexes** - Add indexes on lookup fields
5. **Webhook timeouts** - Respond quickly, process async

### Monitoring
- Check execution times in n8n
- Set up error notifications
- Log important data points

---

## 🔗 Useful Resources

- [n8n Docs - PostgreSQL Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.postgres/)
- [Slack API - chat.postMessage](https://api.slack.com/methods/chat.postMessage)
- [Claude API Docs](https://docs.anthropic.com/claude/reference/messages_post)
- [Let's Encrypt - Certbot](https://certbot.eff.org/)

---

**Last Updated:** 2025-10-16
**Applies to:** n8n v1.115.3, Amazon Linux 2023
