# LinkedIn Poster Agent

An AI-powered LinkedIn content generation system that creates authentic, on-brand posts in Ruby's voice. Built with n8n, Claude AI, and Slack for a seamless content creation workflow.

## 🎯 Overview

This agent automates LinkedIn content creation while maintaining authenticity and brand voice. It features:

- **Daily Auto-Generation** - Automatically generates post ideas every morning at 9am
- **Manual On-Demand Creation** - Generate posts anytime with custom topics via Slack commands
- **AI-Powered Voice Matching** - Uses Claude AI to write in Ruby's authentic style
- **Review & Refinement Workflow** - Easy approval, editing, and posting through Slack
- **Content Analytics** - Track engagement and optimize future posts (Phase 2)

## 🏗️ Architecture

```
┌─────────────┐
│   Slack Bot │ ◄──── User interaction (commands & buttons)
│   "Banto"   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│     n8n     │ ◄──── Workflow orchestration
│  Workflows  │
└──────┬──────┘
       │
       ├──► Claude API ──── Content generation
       ├──► PostgreSQL ─── Data storage
       └──► LinkedIn API ── Publishing (Phase 2)
```

### Key Components

- **Slack Bot (Banto)** - User interface with 10 slash commands
- **n8n Workflows** - Automation and orchestration platform
- **Claude AI** - Content generation with voice matching
- **PostgreSQL** - Database for posts, drafts, and analytics
- **LinkedIn API** - Direct posting (Phase 2)

## ✨ Features

### Content Generation

- **Dual Mode Operation**
  - 🌅 **Daily Auto-Generation**: Wakes up at 9am with fresh post ideas
  - 📝 **Manual Creation**: Generate posts on-demand with `/linkedin-draft [topic]`

- **Authentic Voice Matching**
  - Trained on Ruby's actual LinkedIn posts
  - No AI jargon or corporate buzzwords
  - Conversational, story-driven style
  - Strategic emoji usage (1-2 max)

- **Smart Content Focus**
  - Niche ML use cases
  - GenAI + AWS/GCP solutions
  - AI security & governance
  - Personal journey stories
  - Loop8 client wins

### Workflow Features

- **Review & Approval** - Interactive Slack buttons for quick actions
- **Iterative Refinement** - Request changes until perfect
- **Post Tracking** - All drafts saved with unique IDs
- **Version Control** - Track edits and refinements
- **Engagement Scoring** - 0-10 scale for post quality

## 🚀 Getting Started

### Prerequisites

- n8n instance (self-hosted or cloud)
- PostgreSQL database
- Slack workspace with admin access
- Claude API key (Anthropic)
- LinkedIn account (for Phase 2)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/loop8io/linkedin-poster.git
   cd linkedin-poster
   ```

2. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your credentials
   ```

3. **Create database schema**
   - Import `database-schema.sql` to your PostgreSQL database
   - Or use the n8n workflow: `n8n-workflows/DB_Schema_Setup.json`

4. **Configure Slack Bot**
   - Follow the detailed guide in `SLACK_BOT_SETUP.md`
   - Create app in Slack API portal
   - Configure 10 slash commands
   - Set up OAuth permissions

5. **Import n8n workflows**
   - Import workflows from `n8n-workflows/` directory
   - Configure credentials (Slack, PostgreSQL, Claude API)
   - Activate workflows

6. **Test the system**
   - Run verification workflow to check database
   - Test `/linkedin-draft` command in Slack
   - Review generated content

## 📖 Documentation

- **[DESIGN_PLAN.md](DESIGN_PLAN.md)** - Complete system architecture and design
- **[SLACK_BOT_SETUP.md](SLACK_BOT_SETUP.md)** - Step-by-step Slack bot configuration
- **[N8N_API_REFERENCE.md](N8N_API_REFERENCE.md)** - n8n API usage guide
- **[PROGRESS.md](PROGRESS.md)** - Development progress and decisions
- **[IMPORT_INSTRUCTIONS.md](IMPORT_INSTRUCTIONS.md)** - Workflow import guide

## 🎮 Usage

### Slack Commands

**Manual Post Creation:**
```
/linkedin-draft [topic or idea]
```

**Review & Action:**
```
/linkedin-approve [post-id]     # Approve and post
/linkedin-refine [post-id] [instructions]  # Request changes
/linkedin-regenerate [post-id]  # Generate new version
/linkedin-skip [post-id]        # Discard this draft
```

**Settings:**
```
/linkedin-settings   # Configure auto-generation
/linkedin-pause      # Pause daily posts
/linkedin-resume     # Resume daily posts
/linkedin-help       # Show all commands
```

### Daily Auto-Generation

Every morning at 9am (configurable):
1. Agent fetches latest AI/ML/cloud news
2. Generates 2-3 post ideas
3. Selects best idea and creates full draft
4. Sends to Slack with interactive buttons
5. Waits for your approval

### Interactive Buttons

- ✅ **Approve & Post** - Publish immediately to LinkedIn
- ✏️ **Refine** - Request specific changes
- 💡 **New Idea** - Generate different topic
- 🗑️ **Skip** - Discard this draft
- 📅 **Schedule** - Choose posting time

## 📊 Database Schema

### Tables

- **`linkedin_posts`** - All generated posts and drafts
- **`writing_samples`** - Ruby's existing posts for style training
- **`content_ideas`** - Pool of topics for auto-generation
- **`auto_gen_settings`** - Configuration (schedule, frequency)
- **`post_analytics`** - Engagement metrics (Phase 2)

## 🎨 Content Quality Safeguards

### AI Detection Prevention

**Banned phrases:**
- "Dive into", "Delve into", "Unlock", "Harness"
- "Revolutionary", "Game-changer"
- "In today's fast-paced world"

**Style validators:**
- Max 2 emojis per post
- At least 1 personal pronoun (I, we, my)
- Must include specific example or story
- Conversational tone check

### Engagement Optimization

**Hook patterns:**
- Contrarian statements
- Personal failures/challenges
- Surprising statistics
- Relatable questions

**Structure scoring (0-10 scale):**
- Clear hook (3 pts)
- Story/narrative (3 pts)
- Value/lesson (2 pts)
- Call-to-action (2 pts)

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Orchestration** | n8n | Visual workflow automation |
| **AI Engine** | Claude 3.5 Sonnet | Content generation |
| **Chat Interface** | Slack | User interaction |
| **Database** | PostgreSQL | Data persistence |
| **Hosting** | Self-hosted n8n | Workflow execution |
| **Social API** | LinkedIn API | Post publishing (Phase 2) |

## 📈 Development Phases

### Phase 1: MVP (Current)
- ✅ Database schema & setup
- ✅ Slack bot configuration
- ⏳ n8n workflows (in progress)
- ⏳ Claude API integration
- ⏳ Manual posting workflow

### Phase 2: Enhancement
- LinkedIn API direct posting
- Interactive Slack buttons
- Engagement scoring
- News fetching automation
- Post scheduling

### Phase 3: Advanced
- Engagement metrics tracking
- Analytics dashboard
- A/B testing hooks
- Learning from edits
- Multi-platform support

## 🤝 Contributing

This is a private Loop8 internal tool. If you're on the Loop8 team and want to contribute:

1. Create a feature branch
2. Make your changes
3. Test thoroughly
4. Submit a pull request

## 📝 Project Status

**Current Phase:** MVP Development (Phase 1)
**Completion:** ~40% of MVP
**Next Milestone:** Complete n8n workflows

See [PROGRESS.md](PROGRESS.md) for detailed status.

## 🔒 Security

- All API keys stored in `.env` (never committed)
- Credentials encrypted in n8n
- No sensitive client data in prompts
- Audit log of all generated content
- User confirmation required before posting

## 📄 License

Private - Loop8 Internal Tool

---

**Built with ❤️ by the Loop8 team**

*"Stop doing robot work so you can do human work."*

## 🆘 Support

For issues or questions:
1. Check the documentation in this repo
2. Review [TROUBLESHOOTING.md](SLACK_BOT_SETUP.md#troubleshooting)
3. Contact the Loop8 tech team

---

**Last Updated:** 2025-10-15
**Version:** 0.1.0-alpha
