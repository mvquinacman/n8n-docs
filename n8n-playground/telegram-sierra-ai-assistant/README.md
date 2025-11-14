# Telegram AI Assistant with S.I.E.R.R.A

A sophisticated n8n workflow that creates an intelligent Telegram bot powered by GPT-4, with voice message support and multiple tool integrations.

## Overview

**S.I.E.R.R.A** (Smart Intelligent Entity for Research and Response Automation) is an advanced AI assistant that operates through Telegram, capable of handling both text and voice messages, and equipped with multiple tools for enhanced functionality.

## Features

### Core Capabilities
- **Text & Voice Input**: Process both text messages and voice notes
- **Voice Transcription**: Automatic transcription using OpenAI Whisper
- **Voice Responses**: Generate voice responses using ElevenLabs TTS
- **Conversation Memory**: Maintains context across conversations per chat
- **Multi-Tool Integration**: Access to Gmail, Calendar, Notion, Web Search, and Calculator

### Integrated Tools
1. **Gmail Integration**
   - Search and read emails
   - Send emails

2. **Google Calendar**
   - Read scheduled events
   - Create new calendar events

3. **Notion Search**
   - Query Notion databases
   - Access knowledge base

4. **Web Search (SerpAPI)**
   - Real-time web searches
   - Current information lookup

5. **Calculator**
   - Perform mathematical calculations
   - Complex computations

## Workflow Structure

```
Telegram Trigger
    ↓
Switch (Text/Voice)
    ↓
├─→ Text Path
│   └─→ Extract text & user info
│       └─→ S.I.E.R.R.A Agent
│
└─→ Voice Path
    └─→ Download audio
        └─→ Transcribe (Whisper)
            └─→ Extract transcribed text
                └─→ S.I.E.R.R.A Agent

S.I.E.R.R.A Agent (with tools & memory)
    ↓
Prepare Response
    ↓
├─→ Text Response (Telegram)
└─→ Voice Response (ElevenLabs → Telegram)
```

## Prerequisites

### Required Credentials
1. **Telegram Bot Token**
   - Create a bot via [@BotFather](https://t.me/botfather)
   - Get your bot token

2. **OpenAI API Key**
   - For GPT-4 and Whisper transcription
   - Sign up at [OpenAI](https://platform.openai.com/)

3. **ElevenLabs API Key**
   - For text-to-speech voice generation
   - Sign up at [ElevenLabs](https://elevenlabs.io/)

4. **Optional Service Credentials**
   - Gmail OAuth credentials
   - Google Calendar OAuth credentials
   - Notion API token
   - SerpAPI key

### Environment Variables
```env
ELEVENLABS_VOICE_ID=your_voice_id_here
```

## Setup Instructions

### 1. Import Workflow
1. Open n8n
2. Click "Import from File"
3. Select `workflow.json`
4. The workflow will be imported

### 2. Configure Credentials

#### Telegram
- Node: "Telegram Trigger", "Text response", "Voice response"
- Add your Telegram Bot Token

#### OpenAI
- Nodes: "Transcribe audio", "OpenAI GPT-4"
- Add your OpenAI API Key

#### ElevenLabs
- Node: "Generate voice"
- Configure HTTP Header Authentication
- Header: `xi-api-key`
- Value: Your ElevenLabs API key
- Set environment variable `ELEVENLABS_VOICE_ID`

#### Optional Tools
Configure credentials for any tools you want to use:
- Gmail nodes: Add Gmail OAuth credentials
- Calendar nodes: Add Google Calendar OAuth credentials
- Notion node: Add Notion API token
- SerpAPI node: Add SerpAPI key

### 3. Configure Tool Workflows
The workflow references tool sub-workflows. You have two options:

**Option A**: Remove unused tools
- Delete tool nodes you don't need
- Update S.I.E.R.R.A's system prompt to reflect available tools

**Option B**: Create sub-workflows
- Create separate workflows for each tool
- Update `workflowId` in each tool node
- Ensure sub-workflows return proper responses

### 4. Activate Workflow
1. Test the workflow with a test message
2. Once confirmed working, activate the workflow
3. Set webhook mode to "Production"

## Usage

### Text Commands
Simply send a text message to your bot:
```
What's on my calendar today?
Search my emails for "project update"
Create a meeting for tomorrow at 2pm
Calculate 15% of 250
Search the web for latest AI news
```

### Voice Commands
Send a voice note to your bot. It will:
1. Transcribe your voice message
2. Process the request
3. Send both text and voice responses

## What Was Fixed

This workflow has been optimized with the following improvements:

### 1. Connected Missing Tools
- All tool nodes (Gmail, Calendar, SerpAPI, Notion, Calculator) are now properly connected to the S.I.E.R.R.A agent via `ai_tool` connections

### 2. Voice Flow Completion
- Added "Extract transcribed text" node to properly format transcription output
- Connected voice transcription path to the main S.I.E.R.R.A agent
- Voice messages now flow correctly through the entire pipeline

### 3. ElevenLabs Configuration
- Added proper request body with required parameters:
  - `text`: The response text to convert
  - `model_id`: ElevenLabs model selection
  - `voice_settings`: Stability and similarity boost settings

### 4. Enhanced Tool Descriptions
- Improved tool descriptions for better agent decision-making
- More specific use cases for each tool

### 5. Node Organization
- Repositioned nodes for better visual flow
- Updated node names for clarity
- Added comprehensive notes to all nodes

### 6. Data Flow Optimization
- Fixed chatId passing in voice flow
- Improved response preparation logic
- Better error handling through proper connections

## Customization

### Modify System Prompt
Edit the "🤖 S.I.E.R.R.A" node to customize:
- Agent personality
- Response style
- Tool usage instructions
- Special capabilities

### Adjust Memory Settings
In "Chat Memory" node:
- `contextWindowLength`: Number of messages to remember (default: 10)
- `sessionKey`: How conversations are grouped (default: by chatId)

### Change Voice Settings
In "Generate voice" node:
- `stability`: Voice consistency (0-1)
- `similarity_boost`: Voice similarity (0-1)
- `model_id`: Choose different ElevenLabs models

## Troubleshooting

### Bot Not Responding
1. Check Telegram webhook is active
2. Verify bot token is correct
3. Ensure workflow is activated

### Voice Transcription Fails
1. Verify OpenAI credentials
2. Check audio file format compatibility
3. Ensure sufficient API credits

### Voice Generation Fails
1. Verify ElevenLabs API key
2. Check `ELEVENLABS_VOICE_ID` is set
3. Ensure voice ID is valid

### Tools Not Working
1. Verify tool sub-workflow IDs are correct
2. Check credentials for each service
3. Test tool workflows independently

## Cost Considerations

Running this workflow incurs costs from:
- **OpenAI**: GPT-4 API calls + Whisper transcriptions
- **ElevenLabs**: Text-to-speech generation
- **SerpAPI**: Web searches (if enabled)

Consider:
- Using GPT-3.5-turbo for lower costs
- Disabling voice responses for text-only mode
- Limiting memory context window
- Caching frequent responses

## Security Notes

1. **Never commit credentials** to version control
2. Use environment variables for sensitive data
3. Implement rate limiting for production use
4. Restrict bot access to authorized users
5. Review tool permissions carefully

## Future Enhancements

Potential improvements:
- [ ] Add image processing capabilities
- [ ] Implement document analysis
- [ ] Add database for persistent storage
- [ ] Create admin commands
- [ ] Add user authentication
- [ ] Implement conversation export
- [ ] Add multi-language support
- [ ] Create dashboard for analytics

## License

This workflow is provided as-is for educational and personal use.

## Support

For issues or questions:
1. Check n8n community forums
2. Review n8n documentation
3. Test each node individually
4. Check API service status pages

---

**Version**: 1.0 (Fixed)
**Last Updated**: 2025-11-14
**n8n Version**: Compatible with v1.0+
