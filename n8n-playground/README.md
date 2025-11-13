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
├── social-media-agent/ (example - future workflow)
│   ├── workflow.json
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
**Last Updated:** January 13, 2025
