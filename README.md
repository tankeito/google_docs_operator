English | [中文](README_ZH.md)

# 📄 Google Docs Operator

> **Seamless Google Docs Integration for OpenClaw**
> 
> *Create, read, edit, and export Google Docs with managed OAuth—no local Chrome or complex setup required.*

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/tankeito/google_docs_operator/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-green.svg)](https://openclaw.ai)

---

## 🌟 Project Overview

**Google Docs Operator** is a production-ready OpenClaw skill that provides seamless integration with Google Docs through the **Maton API Gateway**. Say goodbye to complex OAuth configurations, local Chrome drivers, and credential management—just connect your Google account once and start automating your documents.

### What Can You Do?

| Feature | Description |
|---------|-------------|
| 📝 **Document Creation** | Create new Google Docs programmatically with custom titles |
| 📖 **Content Reading** | Extract full document text with paragraph structure preserved |
| ✏️ **Content Editing** | Append, overwrite, or search-and-replace text anywhere in the document |
| 🎨 **Text Formatting** | Apply bold, italic, headings (H1-H6), and paragraph styles |
| 📤 **Document Export** | Export to PDF, DOCX (Word), or TXT formats via Google Drive API |
| 🔗 **Multi-Account Support** | Manage multiple Google account connections with easy switching |

### Why Choose Google Docs Operator?

- **🔐 Managed OAuth**: No need to handle OAuth tokens or service accounts—Maton handles authentication securely
- **⚡ Zero Configuration**: Connect your Google account in minutes via a simple authorization URL
- **🌐 REST API Based**: Uses official Google Docs API v1 with full feature parity
- **🛠️ CLI Tool Included**: Quick command-line operations for common tasks
- **📦 OpenClaw Native**: Designed specifically for OpenClaw AI assistant integration

---

## ⚠️ Prerequisites & Requirements

Before using this skill, ensure you have:

1. **Maton API Key** — Required for all operations
2. **Google Account** — Must authorize via Maton's OAuth flow
3. **Network Access** — All requests go through Maton's gateway
4. **Python 3.8+** — For CLI tool usage (optional for OpenClaw integration)

---

## 🚀 Quick Start

### Step 1: Get Your Maton API Key

1. Sign in or create an account at [maton.ai](https://maton.ai)
2. Navigate to [Settings](https://maton.ai/settings)
3. Copy your API key

```bash
export MATON_API_KEY="your_api_key_here"
```

### Step 2: Install Dependencies

```bash
# Navigate to the skill directory
cd ~/.openclaw/workspace/skills/google_docs_operator

# Install Python requirements
pip install -r requirements.txt
```

### Step 3: Create Google OAuth Connection

```bash
# List existing connections
python3 gdocs_driver.py connections list

# Create new connection (opens authorization URL)
python3 gdocs_driver.py connections create
```

**Authorization Flow**:
1. Run the `connections create` command
2. Copy the authorization URL from the output
3. Open the URL in your browser
4. Sign in with your Google account and grant permissions
5. Return to the skill—your connection is now active!

### Step 4: Test Your Setup

```bash
# Create a test document
python3 gdocs_driver.py create --title "My First Document"

# Read the document (replace DOCUMENT_ID with the output from create)
python3 gdocs_driver.py get --id DOCUMENT_ID
```

---

## 📚 Command Reference

### Core Commands (OpenClaw Integration)

| Command | Description | Example |
|---------|-------------|---------|
| `/gdocs create` | Create a new Google Doc | `/gdocs create "Meeting Notes"` |
| `/gdocs read` | Read document content | `/gdocs read DOCUMENT_ID` |
| `/gdocs write` | Write or append content | `/gdocs write DOCUMENT_ID "New content"` |
| `/gdocs export` | Export document to PDF/DOCX | `/gdocs export DOCUMENT_ID pdf` |

### CLI Commands (Direct Usage)

| Command | Description | Example |
|---------|-------------|---------|
| `get` | Read document content | `python3 gdocs_driver.py get --id DOC_ID` |
| `create` | Create new document | `python3 gdocs_driver.py create --title "Doc Title"` |
| `append` | Append text to end | `python3 gdocs_driver.py append --id DOC_ID --content "Text"` |
| `write` | Overwrite entire document | `python3 gdocs_driver.py write --id DOC_ID --content "New text"` |
| `replace` | Search and replace | `python3 gdocs_driver.py replace --id DOC_ID --find "old" --replace "new"` |
| `heading` | Apply heading style | `python3 gdocs_driver.py heading --id DOC_ID --text "Title" --level 1` |
| `bold` | Apply bold formatting | `python3 gdocs_driver.py bold --id DOC_ID --text "important"` |
| `export` | Export to PDF/DOCX/TXT | `python3 gdocs_driver.py export --id DOC_ID --format pdf --output out.pdf` |

---

## 🔧 API Reference

### Base URL

All API requests go through Maton's gateway:

```
https://gateway.maton.ai/google-docs/v1/documents
```

The gateway proxies requests to `docs.googleapis.com` and automatically injects your OAuth token.

### Authentication

All requests require the Maton API key in the Authorization header:

```http
Authorization: Bearer $MATON_API_KEY
```

### Get Document

```http
GET /google-docs/v1/documents/{documentId}
```

**Python Example**:
```python
import os, json, urllib.request

def get_doc_text(doc_id):
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    doc = json.load(urllib.request.urlopen(req))
    
    # Extract plain text from document structure
    text = ''.join(
        pe.get('textRun', {}).get('content', '')
        for elem in doc.get('body', {}).get('content', [])
        for pe in elem.get('paragraph', {}).get('elements', [])
    )
    return {'title': doc['title'], 'content': text, 'documentId': doc['documentId']}
```

### Create Document

```http
POST /google-docs/v1/documents
Content-Type: application/json

{"title": "My New Document"}
```

**Python Example**:
```python
import os, json, urllib.request

data = json.dumps({'title': 'My New Document'}).encode()
req = urllib.request.Request(
    'https://gateway.maton.ai/google-docs/v1/documents',
    data=data, method='POST')
req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
req.add_header('Content-Type', 'application/json')
doc = json.load(urllib.request.urlopen(req))
print(f"Created: https://docs.google.com/document/d/{doc['documentId']}/edit")
```

### Batch Update (Write, Format, Replace)

All content modifications use `batchUpdate`:

```http
POST /google-docs/v1/documents/{documentId}:batchUpdate
Content-Type: application/json

{"requests": [...]}
```

---

## 🎨 Common Operations

### 📝 Append Text (to end of document)

```python
def append_text(doc_id, text):
    # Get document end index
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    doc = json.load(urllib.request.urlopen(req))
    content = doc.get('body', {}).get('content', [])
    end_index = content[-1].get('endIndex', 1) - 1 if content else 1

    # Insert text at end
    body = json.dumps({'requests': [{'insertText': {
        'location': {'index': end_index},
        'text': text
    }}]}).encode()
    req2 = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req2.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req2.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req2)
    print(f'Appended {len(text)} chars')
```

### 🔍 Search & Replace All

```python
def replace_all(doc_id, find, replace):
    body = json.dumps({'requests': [{'replaceAllText': {
        'containsText': {'text': find, 'matchCase': True},
        'replaceText': replace
    }}]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    result = json.load(urllib.request.urlopen(req))
    n = result['replies'][0].get('replaceAllText', {}).get('occurrencesChanged', 0)
    print(f'Replaced {n} occurrences')
```

### 📑 Apply Heading Style

Named styles: `NORMAL_TEXT` | `HEADING_1` ~ `HEADING_6` | `TITLE` | `SUBTITLE`

```python
def apply_heading(doc_id, start_idx, end_idx, level):
    style_map = {1: "HEADING_1", 2: "HEADING_2", 3: "HEADING_3"}
    body = json.dumps({'requests': [{
        'updateParagraphStyle': {
            'range': {'startIndex': start_idx, 'endIndex': end_idx},
            'paragraphStyle': {'namedStyleType': style_map.get(level, 'HEADING_1')},
            'fields': 'namedStyleType'
        }
    }]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req)
    print(f'Applied Heading {level}')
```

### ✨ Apply Text Formatting (Bold / Italic)

```python
def apply_bold(doc_id, start_idx, end_idx):
    body = json.dumps({'requests': [{
        'updateTextStyle': {
            'range': {'startIndex': start_idx, 'endIndex': end_idx},
            'textStyle': {'bold': True},
            'fields': 'bold'
        }
    }]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req)
    print('Applied bold formatting')
```

### 📤 Export to PDF / DOCX

Exports go through the Google Drive API (also proxied by Maton):

```python
def export_doc(doc_id, mime_type, output_path):
    import urllib.parse
    url = (f'https://gateway.maton.ai/google-drive/drive/v3/files/{doc_id}/export'
           f'?mimeType={urllib.parse.quote(mime_type)}')
    req = urllib.request.Request(url)
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    with urllib.request.urlopen(req) as resp:
        with open(output_path, 'wb') as f:
            f.write(resp.read())
    print(f'Exported to {output_path}')

# Export as PDF
export_doc('YOUR_DOC_ID', 'application/pdf', '/tmp/document.pdf')

# Export as Word
export_doc('YOUR_DOC_ID',
           'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
           '/tmp/document.docx')
```

---

## 🔗 Connection Management

Manage your Google OAuth connections at `https://ctrl.maton.ai`.

### List Active Connections

```bash
python3 gdocs_driver.py connections list
```

### Create New Connection

```bash
python3 gdocs_driver.py connections create
```

Output will include an authorization URL—open it in your browser to complete OAuth flow.

### Using Multiple Accounts

If you have multiple Google accounts connected, specify which one:

```python
req.add_header('Maton-Connection', 'YOUR_CONNECTION_ID')
```

---

## 📋 Environment Variables

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `MATON_API_KEY` | ✅ Yes | Your Maton API key from maton.ai/settings | `your_api_key_here` |

---

## 📦 Project Structure

```
google_docs_operator/
├── gdocs_driver.py           # CLI tool for common operations
├── SKILL.md                  # OpenClaw skill definition
├── README.md                 # This file (English documentation)
├── README_ZH.md              # Chinese documentation
├── LICENSE                   # MIT License
├── requirements.txt          # Python dependencies
└── .gitignore                # Git ignore rules
```

---

## ❓ FAQ

### Q: How do I extract the Document ID from a URL?

**A**: From a Google Docs URL like:
```
https://docs.google.com/document/d/1a2b3c4d5e6f7g8h9i0j/edit
```
The Document ID is: `1a2b3c4d5e6f7g8h9i0j` (the string between `/d/` and `/edit`)

### Q: Can I use this with Google Workspace accounts?

**A**: Yes! The Maton gateway supports both personal Gmail and Google Workspace accounts.

### Q: What happens if my OAuth token expires?

**A**: Maton handles token refresh automatically. If a connection becomes invalid, you'll receive a 401 error—simply recreate the connection.

### Q: Is there a rate limit?

**A**: Google Docs API enforces 10 requests/second per account. Maton may also apply rate limiting based on your plan.

---

## ⚠️ Error Handling

| Status Code | Meaning | Solution |
|-------------|---------|----------|
| 400 | Missing connection or malformed request | Check document ID and request body |
| 401 | Invalid or missing Maton API key | Verify `MATON_API_KEY` environment variable |
| 403 | Insufficient permissions | Ensure OAuth connection has document access |
| 429 | Rate limited | Wait and retry (10 req/sec limit) |
| 4xx/5xx | Google API error | Check Google Docs API status |

---

## 🔐 Security & Privacy

- **OAuth tokens are managed by Maton**—never stored locally
- **All requests are encrypted** via HTTPS
- **No local Chrome or browser automation** required
- **`.gitignore` protects sensitive files**—never commit API keys

**Best Practices**:
1. Never commit `MATON_API_KEY` to version control
2. Use environment variables or secure secret management
3. Regularly rotate API keys via Maton dashboard
4. Audit connection access at `ctrl.maton.ai`

---

## 🔄 Changelog

### v1.0.0 — 2026-03-16

- 🎉 **Initial Release**: Full Google Docs API integration via Maton Gateway
- 📝 **Core Operations**: Create, read, append, write, search & replace
- 🎨 **Formatting Support**: Headings (H1-H6), bold, italic, paragraph styles
- 📤 **Export Functionality**: PDF, DOCX, TXT via Google Drive API
- 🛠️ **CLI Tool**: `gdocs_driver.py` for quick command-line operations
- 🔗 **Multi-Account**: Support for multiple Google account connections
- 📚 **Documentation**: Complete English & Chinese README

---

## 📄 License

MIT License – See [LICENSE](LICENSE) for details.

---

## 📞 Support & Resources

- **GitHub Issues**: https://github.com/tankeito/google_docs_operator/issues
- **Maton Documentation**: https://maton.ai/docs
- **Google Docs API**: https://developers.google.com/workspace/docs/api
- **Maton Community**: https://discord.com/invite/dBfFAcefs2
- **Maton Support**: support@maton.ai

---

## 🙏 Acknowledgments

- **Maton** – For providing the OAuth-managed API gateway
- **Google Workspace** – For the powerful Docs API
- **OpenClaw** – For the AI assistant platform
