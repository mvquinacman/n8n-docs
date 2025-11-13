# RAG Agent Chatbot for Document Interaction

A comprehensive n8n workflow that implements Retrieval-Augmented Generation (RAG) with chatbot functionality, allowing users to interact naturally with various document types through an AI-powered conversational interface.

## Features

### Multi-Format Document Support
- **PDF Documents**: Research papers, reports, manuals
- **Word Documents (DOCX)**: Business documents, articles
- **Text Files (TXT)**: Plain text documents
- **Markdown (MD)**: Technical documentation
- **CSV**: Structured data files
- And more formats supported by n8n's Extract from File node

### Advanced RAG Capabilities
- **Semantic Search**: Uses OpenAI embeddings for intelligent document retrieval
- **Vector Database**: Powered by Qdrant for efficient similarity search
- **Context-Aware Responses**: AI agent retrieves relevant document chunks before answering
- **Source Citations**: Responses include references to source documents
- **Conversation Memory**: Maintains context across multiple chat turns

### User-Friendly Interface
- **Simple Document Upload**: Easy ingestion pipeline
- **Interactive Chat**: Natural language Q&A interface
- **Real-time Responses**: Fast retrieval and generation
- **Multi-Document Support**: Query across all uploaded documents

## Architecture

### Part 1: Document Ingestion Pipeline

```
Manual Trigger → Set Document Path → Read Binary File → Extract from File
→ Add Metadata → Vector Store (Insert) + Embeddings + Text Splitter
```

**Components**:
1. **Manual Trigger**: Initiates the document upload process
2. **Set Document Path**: Specify the file path and name
3. **Read Binary File**: Reads the document into memory
4. **Extract from File**: Extracts text content from various formats
5. **Add Metadata**: Tags documents with name, timestamp, file type
6. **Text Splitter**: Breaks documents into 2000-character chunks with 200-char overlap
7. **OpenAI Embeddings**: Converts text chunks to vector embeddings (1536 dimensions)
8. **Qdrant Vector Store**: Stores embeddings for semantic search

### Part 2: Chat Interface with RAG

```
Chat Trigger → AI Agent ← OpenAI Chat Model
                      ← Conversation Memory
                      ← Vector Store Tool → Qdrant Retrieval + Embeddings
```

**Components**:
1. **Chat Trigger**: Webhook-based chat interface
2. **AI Agent**: Orchestrates the RAG process
3. **OpenAI Chat Model**: GPT-4o-mini for response generation
4. **Conversation Memory**: Window buffer for multi-turn conversations
5. **Vector Store Tool**: Retrieves relevant document chunks
6. **Qdrant Retrieval**: Semantic search across indexed documents
7. **OpenAI Embeddings**: Converts user queries to vectors for search

## Setup Instructions

### Prerequisites
- n8n instance (self-hosted or cloud)
- Qdrant account (free tier available)
- OpenAI API account

### Step 1: Qdrant Configuration

1. **Create Qdrant Account**:
   - Visit https://cloud.qdrant.io
   - Sign up for a free account

2. **Create Collection**:
   - Collection name: `documents`
   - Vector size: `1536` (for OpenAI text-embedding-3-small)
   - Distance metric: `Cosine`
   - Optional: Enable payload indexing for faster metadata filtering

3. **Get Credentials**:
   - Copy your API key from the dashboard
   - Note your cluster URL (e.g., `https://xyz.cloud.qdrant.io:6333`)

### Step 2: OpenAI Configuration

1. **Get API Key**:
   - Visit https://platform.openai.com
   - Navigate to API Keys section
   - Create a new API key

2. **Cost Estimation**:
   - Embeddings (text-embedding-3-small): ~$0.0001 per 1K tokens
   - Chat (gpt-4o-mini): ~$0.15 per 1M input tokens, ~$0.60 per 1M output tokens

### Step 3: n8n Workflow Setup

1. **Import Workflow**:
   - Download `RAG Agent Chatbot for Document Interaction.json`
   - In n8n, go to Workflows → Import from File
   - Select the downloaded JSON file

2. **Configure Credentials**:

   **Qdrant API Credentials**:
   - Go to Credentials → Add Credential
   - Select "Qdrant API"
   - Enter your API key and cluster URL
   - Save with name "Qdrant API"

   **OpenAI API Credentials**:
   - Go to Credentials → Add Credential
   - Select "OpenAI API"
   - Enter your API key
   - Save with name "OpenAI API"

3. **Update Node Credentials**:
   - Click on each node with credential requirements
   - Select the appropriate credential from the dropdown
   - Nodes requiring credentials:
     - Insert into Qdrant Vector Store
     - OpenAI Embeddings (2 instances)
     - Retrieve from Qdrant
     - OpenAI Chat Model

4. **Verify Collection Name**:
   - Ensure all Qdrant nodes reference the same collection: `documents`

## Usage Guide

### Uploading Documents

1. **Prepare Your Document**:
   - Save your document in a location accessible to n8n
   - Note the full file path

2. **Configure Upload**:
   - Click on "Set Document Path" node
   - Update `file_path` field with your document's full path
   - Update `file_name` field with a descriptive name

3. **Execute Ingestion**:
   - Click "Execute Workflow" button on the manual trigger
   - Wait for completion (typically 10-60 seconds depending on document size)
   - Check for successful execution

4. **Upload Multiple Documents**:
   - Repeat steps 2-3 for each document
   - All documents are stored in the same vector database
   - You can query across all uploaded documents

### Chatting with Documents

1. **Start Chat Session**:
   - Click the "Chat" button on the Chat Trigger node
   - A chat interface will open

2. **Ask Questions**:
   - Type your question in natural language
   - Examples:
     - "What are the main findings in the research paper?"
     - "Summarize the key points from all documents"
     - "What does the contract say about payment terms?"
     - "Compare the methodologies across the uploaded papers"

3. **Conversation Features**:
   - **Follow-up Questions**: The agent remembers previous messages
   - **Multi-turn Dialogue**: Have natural back-and-forth conversations
   - **Source Attribution**: Responses include document references
   - **Clarification**: Ask for more details or explanations

4. **Example Conversations**:

   **Example 1: Research Paper Analysis**
   ```
   User: What is this paper about?
   AI: Based on the uploaded document "research_paper.pdf", this paper presents...

   User: What methodology did they use?
   AI: The researchers employed a mixed-methods approach, specifically...

   User: What were the limitations?
   AI: The paper acknowledges several limitations: 1) ...
   ```

   **Example 2: Multi-Document Query**
   ```
   User: Summarize all the uploaded documents
   AI: I have access to 3 documents in the knowledge base:
   1. "contract.pdf" - A service agreement outlining...
   2. "proposal.docx" - A business proposal for...
   3. "notes.txt" - Meeting notes discussing...
   ```

## Advanced Configuration

### Customizing Chunk Size

In the "Recursive Text Splitter" node:
- **chunkSize**: Default 2000 characters
  - Increase for documents with longer coherent sections
  - Decrease for more granular retrieval
- **chunkOverlap**: Default 200 characters
  - Maintains context across chunk boundaries
  - Increase for documents with complex references

### Adjusting Retrieval Parameters

In the "Retrieve from Qdrant" node:
- **topK**: Default 5 chunks
  - Increase for more comprehensive context
  - Decrease to reduce token usage and improve response time
  - Range: 1-20 recommended

### Modifying AI Agent Behavior

In the "RAG AI Agent" node, customize the system message:
```
You are a helpful AI assistant with access to a knowledge base...
```

**Customization Examples**:
- **Academic Focus**: "You are an academic research assistant..."
- **Legal Focus**: "You are a legal document analyst..."
- **Technical Focus**: "You are a technical documentation expert..."

### Changing LLM Model

In the "OpenAI Chat Model" node:
- **gpt-4o-mini**: Default, cost-effective, fast
- **gpt-4o**: More powerful, better reasoning
- **gpt-3.5-turbo**: Cheaper, faster, less capable

### Memory Configuration

In the "Conversation Memory" node:
- Uses session-based memory (preserves context per user)
- Alternative: Use persistent memory with database storage
- Configure window size for longer/shorter context retention

## Troubleshooting

### Common Issues

**Issue 1: "Collection not found" Error**
- **Cause**: Qdrant collection doesn't exist
- **Solution**: Create collection in Qdrant dashboard with correct name and dimensions

**Issue 2: Document Extraction Fails**
- **Cause**: Unsupported file format or corrupted file
- **Solution**: Verify file integrity and format compatibility

**Issue 3: Chat Returns Generic Answers**
- **Cause**: No documents uploaded or retrieval failing
- **Solution**:
  - Execute document ingestion workflow first
  - Check Qdrant collection has vectors
  - Verify credential configuration

**Issue 4: High API Costs**
- **Cause**: Large documents or frequent queries
- **Solution**:
  - Reduce chunk size and topK
  - Use gpt-3.5-turbo instead of gpt-4
  - Implement rate limiting

**Issue 5: Slow Response Times**
- **Cause**: Large retrieval set or complex queries
- **Solution**:
  - Reduce topK value
  - Optimize chunk size
  - Use faster embedding model

## Performance Optimization

### For Large Documents (>100 pages)

1. **Increase Chunk Overlap**: 300-400 characters
2. **Use Hierarchical Chunking**: Pre-process with section headers
3. **Add Metadata Filtering**: Tag documents with categories
4. **Implement Caching**: Cache frequent queries

### For High Query Volume

1. **Use Faster Models**: gpt-3.5-turbo or gpt-4o-mini
2. **Implement Query Batching**: Process multiple queries together
3. **Enable Qdrant Query Caching**: Configure in Qdrant settings
4. **Reduce topK**: Start with 3 chunks

### For Better Accuracy

1. **Increase topK**: Use 7-10 chunks
2. **Use Better Models**: gpt-4o for complex reasoning
3. **Improve Chunking**: Use semantic chunking strategies
4. **Add Re-ranking**: Implement a re-ranking step after retrieval

## Security Considerations

1. **API Key Protection**:
   - Never commit API keys to version control
   - Use n8n's credential management system
   - Rotate keys regularly

2. **Document Access Control**:
   - Implement user authentication for chat interface
   - Use separate collections for different user groups
   - Enable audit logging

3. **Data Privacy**:
   - Documents are sent to OpenAI for embedding and processing
   - Consider using local LLMs for sensitive data
   - Review OpenAI's data usage policies

## Cost Estimation

### Example: 100-page PDF Document

**Ingestion (one-time)**:
- Extraction: Free (n8n built-in)
- Embeddings: ~50,000 tokens = $0.005
- Storage: Free (Qdrant free tier: 1GB)

**Per Query**:
- Query embedding: ~50 tokens = $0.000005
- LLM generation (gpt-4o-mini): ~1,000 tokens = $0.00015-$0.0006

**Monthly Estimate (100 queries/day)**:
- Embeddings: ~$0.02
- LLM usage: ~$4.50-$18
- **Total: ~$5-$20/month** (plus one-time ingestion)

## Extending the Workflow

### Add Document Update Functionality

Create a separate workflow to handle document updates:
1. Delete old document vectors by ID
2. Re-process updated document
3. Insert new vectors with same metadata

### Implement Citation Extraction

Modify the AI Agent prompt to include specific citations:
```
When answering, always provide citations in the format:
[Document Name, Page/Section]
```

### Add Multi-Modal Support

Extend to handle images using:
- GPT-4 Vision for image understanding
- CLIP embeddings for image search
- Combined text+image retrieval

### Create Analytics Dashboard

Track usage metrics:
- Number of documents indexed
- Query patterns and frequency
- Response quality ratings
- Popular documents

## Integration Ideas

### Slack Integration
- Add Slack trigger instead of Chat Trigger
- Users can query documents from Slack
- Responses posted in threads

### Web Application
- Use n8n webhook as backend API
- Build custom frontend with React/Vue
- Implement user authentication

### Email Processing
- Auto-process email attachments
- Index incoming documents automatically
- Send digest summaries

### CRM Integration
- Link to customer records
- Query customer-specific documents
- Automated report generation

## Best Practices

1. **Document Organization**:
   - Use consistent naming conventions
   - Add descriptive metadata
   - Organize by category/topic

2. **Query Formulation**:
   - Be specific in questions
   - Provide context when needed
   - Use follow-up questions for clarification

3. **Maintenance**:
   - Regularly review and update documents
   - Monitor API usage and costs
   - Clean up outdated vectors

4. **Quality Assurance**:
   - Test with known questions
   - Verify source attribution
   - Review generated responses for accuracy

## Support and Resources

### Documentation
- n8n Documentation: https://docs.n8n.io
- Qdrant Documentation: https://qdrant.tech/documentation
- OpenAI API Reference: https://platform.openai.com/docs

### Community
- n8n Community Forum: https://community.n8n.io
- n8n Discord: https://discord.gg/n8n

### Related Workflows
- Building RAG Chatbot for Movie Recommendations
- Chat with PDF docs using AI (quoting sources)
- Ask questions about a PDF using AI

## License

This workflow is provided as-is for use with n8n. Ensure compliance with:
- OpenAI's Terms of Service
- Qdrant's Terms of Service
- Your organization's data policies

## Version History

- **v1.0** (2025-01-13): Initial release
  - Multi-format document support
  - Qdrant integration
  - OpenAI embeddings and chat
  - Conversation memory
  - Source attribution

## Contributing

Improvements and suggestions welcome! Consider:
- Adding support for more vector databases
- Implementing advanced chunking strategies
- Creating UI enhancements
- Optimizing performance

---

**Created by**: Claude AI Assistant
**Date**: January 13, 2025
**Workflow Type**: AI/RAG/Chatbot
**n8n Version**: Compatible with n8n v1.0+
