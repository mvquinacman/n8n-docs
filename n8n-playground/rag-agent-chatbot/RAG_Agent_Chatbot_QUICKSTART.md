# Quick Start Guide: RAG Agent Chatbot

Get up and running with the RAG Agent Chatbot in 10 minutes!

## 🚀 5-Minute Setup

### Step 1: Get Your API Keys (2 minutes)

**Qdrant** (Vector Database):
1. Go to https://cloud.qdrant.io/login
2. Sign up for free account
3. Click "Create Cluster" → Select free tier
4. Create collection:
   - Name: `documents`
   - Vector size: `1536`
   - Distance: `Cosine`
5. Copy API key from dashboard

**OpenAI** (AI Models):
1. Go to https://platform.openai.com/api-keys
2. Sign in or create account
3. Click "Create new secret key"
4. Copy the key (you won't see it again!)

### Step 2: Import Workflow (1 minute)

1. Download `RAG Agent Chatbot for Document Interaction.json`
2. In n8n: Workflows → Three dots menu → Import from File
3. Select the JSON file
4. Click "Import"

### Step 3: Add Credentials (2 minutes)

**Add Qdrant Credentials**:
1. Click any yellow "Qdrant" node
2. Click "Create New Credential"
3. Enter:
   - Name: "Qdrant API"
   - API Key: [your key]
   - URL: [your cluster URL]
4. Click "Save"

**Add OpenAI Credentials**:
1. Click any yellow "OpenAI" node
2. Click "Create New Credential"
3. Enter:
   - Name: "OpenAI API"
   - API Key: [your key]
4. Click "Save"

## 📄 First Document Upload (2 minutes)

1. **Prepare a test document**:
   - Save any PDF, DOCX, TXT, or MD file
   - Note the full file path (e.g., `/home/user/documents/test.pdf`)

2. **Configure upload node**:
   - Click "Set Document Path" node
   - Change `file_path` to your document path
   - Change `file_name` to your document name

3. **Upload**:
   - Click "Execute Workflow" button (top section)
   - Wait 10-30 seconds
   - Green checkmarks = success!

## 💬 Start Chatting (1 minute)

1. **Open chat**:
   - Click "Chat" button on "Chat Trigger - Ask Questions" node
   - Chat window opens

2. **Ask a question**:
   ```
   What is this document about?
   ```

3. **Get AI response**:
   - AI retrieves relevant content
   - Generates informed answer
   - Includes source citations

## 📋 Example Workflow

### Example 1: Research Paper

**Upload**: `research_paper.pdf`

**Questions**:
```
User: Summarize the main findings
AI: This research paper investigates... The key findings are:
1. [Finding 1]
2. [Finding 2]
3. [Finding 3]

User: What methodology did they use?
AI: The researchers employed a quantitative approach using...

User: What are the limitations?
AI: The paper acknowledges several limitations including...
```

### Example 2: Business Documents

**Upload**: `contract.pdf`, `proposal.docx`, `meeting_notes.txt`

**Questions**:
```
User: What documents do I have?
AI: You have 3 documents:
- contract.pdf: Service agreement...
- proposal.docx: Business proposal...
- meeting_notes.txt: Notes from...

User: What are the payment terms in the contract?
AI: According to contract.pdf, the payment terms are...

User: Compare the proposal with the meeting notes
AI: Comparing the proposal and meeting notes...
```

## 🔧 Common Issues & Fixes

### Issue: "Collection not found"
**Fix**:
- Go to Qdrant dashboard
- Create collection named `documents`
- Set vector size to `1536`

### Issue: "Invalid API key"
**Fix**:
- Double-check API keys (no extra spaces)
- Ensure keys are active
- Try regenerating keys

### Issue: "File not found"
**Fix**:
- Use absolute file path: `/full/path/to/file.pdf`
- Check file exists and is readable
- Verify n8n has file access permissions

### Issue: Chat gives generic answers
**Fix**:
- Upload documents first (top section)
- Verify documents in Qdrant collection
- Check Qdrant credentials are correct

## 💡 Pro Tips

1. **Better Questions**: Be specific
   - ❌ "Tell me about this"
   - ✅ "What are the key recommendations in section 3?"

2. **Upload Multiple Documents**: Run ingestion multiple times
   - Each execution adds a new document
   - Query across all uploaded documents

3. **Conversation Context**: Follow-up questions work!
   - "Tell me more about that"
   - "Can you elaborate?"
   - "What else?"

4. **Cost Savings**:
   - Use smaller chunks for large documents
   - Reduce `topK` from 5 to 3
   - Use gpt-3.5-turbo instead of gpt-4

## 📊 What's Happening Behind the Scenes?

### Document Upload:
1. ✅ Read file → Extract text
2. ✅ Split into chunks (2000 chars)
3. ✅ Convert to vectors (embeddings)
4. ✅ Store in Qdrant database

### Chat Query:
1. ✅ Convert question to vector
2. ✅ Search similar chunks (top 5)
3. ✅ Send chunks + question to AI
4. ✅ Generate informed response

## 🎯 Next Steps

1. **Upload Your Documents**:
   - PDFs, Word docs, text files
   - Multiple documents supported
   - Organize by topic

2. **Customize System Prompt**:
   - Edit "RAG AI Agent" node
   - Change behavior and tone
   - Add domain expertise

3. **Adjust Parameters**:
   - Chunk size: 1000-4000 chars
   - topK: 3-10 chunks
   - Model: gpt-3.5 / gpt-4o-mini / gpt-4o

4. **Integrate**:
   - Add Slack trigger
   - Create web interface
   - Connect to other workflows

## 📚 Full Documentation

For detailed information, see `RAG_Agent_Chatbot_README.md`:
- Advanced configuration
- Security best practices
- Performance optimization
- Integration examples
- Troubleshooting guide

## 🆘 Need Help?

- **n8n Community**: https://community.n8n.io
- **n8n Discord**: https://discord.gg/n8n
- **Documentation**: See full README

## ✅ Checklist

Before asking for help, verify:
- [ ] Qdrant collection created with correct settings
- [ ] API keys are valid and saved in n8n
- [ ] All nodes have credentials assigned
- [ ] File paths are absolute and accessible
- [ ] Document was successfully uploaded (green checkmarks)
- [ ] Chat trigger is activated

---

**Ready to go?** Click "Execute Workflow" and start uploading! 🚀
