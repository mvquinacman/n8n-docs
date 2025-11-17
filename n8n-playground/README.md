# n8n Playground

This folder contains custom n8n workflows created for experimentation and development purposes. These workflows are kept separate from the main collection to maintain the integrity of the original data files.

## Purpose

The **n8n-playground** serves as a dedicated space for:
- Custom workflow development
- Experimental implementations
- New workflow prototypes
- Testing and iteration

All files in this folder are standalone additions and do not modify or interfere with the original workflow templates in the main repository.

## Folder Structure

Each workflow has its own dedicated folder for better organization:

```
n8n-playground/
├── README.md (this file)
├── rag-agent-chatbot/
│   ├── RAG Agent Chatbot for Document Interaction.json
│   ├── RAG_Agent_Chatbot_README.md
│   └── RAG_Agent_Chatbot_QUICKSTART.md
├── facebook-ai-news-bot/
│   ├── Facebook AI News Bot.json
│   ├── README.md
│   └── QUICKSTART.md
└── (more workflows...)
```

## Current Workflows

### 1. RAG Agent Chatbot (`rag-agent-chatbot/`)

A comprehensive Retrieval-Augmented Generation (RAG) chatbot workflow that enables natural language interaction with documents.

**Location:** [`rag-agent-chatbot/`](./rag-agent-chatbot/)

**Features:**
- Multi-format document support (PDF, DOCX, TXT, MD, CSV)
- Vector database integration with Qdrant
- OpenAI embeddings and GPT-4o-mini
- Interactive chat interface with conversation memory
- Semantic search and source attribution

**Quick Start:** See [`rag-agent-chatbot/RAG_Agent_Chatbot_QUICKSTART.md`](./rag-agent-chatbot/RAG_Agent_Chatbot_QUICKSTART.md)

**Documentation:** See [`rag-agent-chatbot/RAG_Agent_Chatbot_README.md`](./rag-agent-chatbot/RAG_Agent_Chatbot_README.md)

---

### 2. Facebook AI News Bot (`facebook-ai-news-bot/`)

An automated social media bot that monitors AI-related subreddits, generates engaging content using AI, and automatically posts to your Facebook page.

**Location:** [`facebook-ai-news-bot/`](./facebook-ai-news-bot/)

**Features:**
- Monitors 8 AI-focused subreddits (r/artificial, r/MachineLearning, etc.)
- Fetches trending posts every 6 hours
- AI-powered content generation with GPT-4o-mini
- Automatic Facebook page posting
- Quality filtering (50+ upvotes)
- Hashtag generation and engagement optimization
- Zero manual intervention required

**Quick Start:** See [`facebook-ai-news-bot/QUICKSTART.md`](./facebook-ai-news-bot/QUICKSTART.md)

**Documentation:** See [`facebook-ai-news-bot/README.md`](./facebook-ai-news-bot/README.md)

---

### 3. Email and Document Automation (`email-document-automation/`)

A comprehensive workflow for automated email processing, AI-powered classification, document storage on NAS, invoice validation, and project tracking.

**Location:** [`email-document-automation/`](./email-document-automation/)

**Features:**
- Multi-account IMAP email monitoring (3+ accounts)
- AI-powered email classification (Invoice, Project Update, Document, General)
- Automatic NAS/SMB document storage with organized folder structure
- Semi-automated invoice validation with AI checking
- Project tracking spreadsheet updates
- PDF generation and templated email responses
- Comprehensive logging and error alerting (Slack, Email, Google Sheets)
- Automatic folder creation following customizable logic
- Support for multiple attachments and file types

**Quick Start:** See [`email-document-automation/QUICKSTART.md`](./email-document-automation/QUICKSTART.md)

**Documentation:** See [`email-document-automation/README.md`](./email-document-automation/README.md)

**Setup Guide:** See [`email-document-automation/SETUP.md`](./email-document-automation/SETUP.md)

---

## Usage

1. Navigate to the desired workflow folder
2. Import the workflow JSON file into your n8n instance
3. Follow the accompanying documentation for setup instructions
4. Configure credentials as needed
5. Start using the workflow

## Adding New Workflows

When adding a new workflow to the playground, please follow this structure:

1. **Create a dedicated folder** with a descriptive name (use kebab-case)
   ```bash
   mkdir n8n-playground/your-workflow-name/
   ```

2. **Add the following files:**
   - `workflow-name.json` - The n8n workflow file
   - `README.md` - Comprehensive documentation
   - `QUICKSTART.md` - Quick start guide (optional but recommended)

3. **Update this README** - Add an entry in the "Current Workflows" section

### Example Structure:
```
your-workflow-name/
├── Your Workflow Name.json     # Main workflow file
├── README.md                   # Full documentation
└── QUICKSTART.md              # Quick start guide (optional)
```

## Contributing

Feel free to add new workflows to this playground folder. Each workflow should:
- Have its own dedicated folder
- Include comprehensive documentation
- Follow the structure outlined above
- Not modify any original repository files

## Note

These workflows are separate from the main n8n template collection and are maintained independently.

---

**Maintained by:** n8n Community Contributors
**Last Updated:** January 17, 2025
