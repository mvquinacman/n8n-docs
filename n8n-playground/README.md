# n8n Playground

This folder contains custom n8n workflows created for experimentation and development purposes. These workflows are kept separate from the main collection to maintain the integrity of the original data files.

## Purpose

The **n8n-playground** serves as a dedicated space for:
- Custom workflow development
- Experimental implementations
- New workflow prototypes
- Testing and iteration

All files in this folder are standalone additions and do not modify or interfere with the original workflow templates in the main repository.

## Current Workflows

### RAG Agent Chatbot for Document Interaction

A comprehensive Retrieval-Augmented Generation (RAG) chatbot workflow that enables natural language interaction with documents.

**Files:**
- `RAG Agent Chatbot for Document Interaction.json` - Main workflow file
- `RAG_Agent_Chatbot_README.md` - Comprehensive documentation
- `RAG_Agent_Chatbot_QUICKSTART.md` - Quick start guide

**Features:**
- Multi-format document support (PDF, DOCX, TXT, MD, CSV)
- Vector database integration with Qdrant
- OpenAI embeddings and GPT-4o-mini
- Interactive chat interface with conversation memory
- Semantic search and source attribution

**Quick Start:** See `RAG_Agent_Chatbot_QUICKSTART.md`

## Usage

1. Import the desired workflow JSON file into your n8n instance
2. Follow the accompanying documentation for setup instructions
3. Configure credentials as needed
4. Start using the workflow

## Contributing

Feel free to add new workflows to this playground folder. Please include:
- The workflow JSON file
- Documentation (README.md)
- Quick start guide (optional but recommended)

## Note

These workflows are separate from the main n8n template collection and are maintained independently.

---

**Maintained by:** n8n Community Contributors
**Last Updated:** January 13, 2025
