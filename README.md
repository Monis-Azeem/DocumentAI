# DocumentAI

A WhatsApp chatbot that lets users upload documents (PDF, JPEG, PNG) and ask AI-powered questions about them. Built with Twilio for WhatsApp messaging and Google Gemini for document analysis.

## Tech Stack

- **Node.js** with **Express.js** - Web server
- **Twilio** - WhatsApp messaging integration
- **Google Generative AI (Gemini 1.5 Flash)** - Document analysis
- **Axios** - HTTP client for downloading media from Twilio
- **node-downloader-helper** - File download utility
- **dotenv** - Environment variable management

## Project Structure

```
DocumentAI/
├── index.js              # Express server, Twilio webhook, session & message handling
├── document_download.js  # Downloads uploaded documents from Twilio media URLs
├── gemini.js             # Uploads documents to Gemini and queries the AI model
├── delete_file.js        # Cleans up locally stored documents
├── package.json
└── .env                  # Environment variables (not committed)
```

## Setup

1. Install dependencies:
   ```bash
   npm install
   ```

2. Create a `.env` file with the following variables:
   ```
   TWILIO_ACCOUNT_SID=your_twilio_account_sid
   TWILIO_AUTH_TOKEN=your_twilio_auth_token
   TWILIO_WHATSAPP_NUMBER=your_twilio_whatsapp_number
   GEMINI_API_KEY=your_google_gemini_api_key
   ```

3. Start the server:
   ```bash
   node index.js
   ```

   The server runs on port **3000**.

4. Configure your Twilio WhatsApp sandbox (or production number) to point the incoming message webhook to:
   ```
   POST https://<your-server>/hello
   ```

## How It Works

```
User sends WhatsApp message
        |
        v
Twilio webhook --> index.js (POST /hello)
        |
        v
Session state machine:
  1. upload_request  -->  User sends first message, gets welcome prompt
  2. enter_prompt    -->  User uploads PDF/JPEG/PNG, file is downloaded locally
  3. process_file    -->  User sends a question, Gemini analyzes the document
        |
        v
Response sent back via Twilio WhatsApp API
```

### User Flow

1. User sends any message to start -- receives a welcome message
2. User uploads a document (PDF, JPEG, or PNG, up to 1GB)
3. Bot confirms upload and asks for a prompt
4. User asks a question about the document
5. Bot returns the AI-generated response
6. User can keep asking questions about the same document
7. User sends `start` to begin a new session with a different document

## API

### `POST /hello`

Twilio WhatsApp webhook endpoint. Receives incoming messages and media attachments.

**Parsed fields from Twilio:**
- `From` - Sender's WhatsApp number
- `Body` - Message text
- `MediaUrl0` - URL of the attached file (if any)

## Supported File Types

| Type | MIME Type |
|------|-----------|
| PDF  | `application/pdf` |
| JPEG | `image/jpeg` |
| PNG  | `image/png` |

Max file size: **1 GB**
