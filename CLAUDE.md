# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static frontend for a RAG (Retrieval-Augmented Generation) financial Q&A assistant. No build step — served as plain HTML/CSS/JS files. The backend API runs at `http://localhost:8086`.

## How to Run

Open `index.html` directly in a browser, or serve with any static file server:

```bash
python3 -m http.server 3000
```

No npm, no bundler, no tests.

## Architecture

- **index.html** — Single-page app shell: sidebar (session list, management links) + chat pane (messages, composer)
- **script.js** — All application logic: session management, message rendering, API calls. Single file, no modules.
- **styles.css** — Full styling with CSS custom properties (deep sea dark theme). Uses Inter font from Google Fonts.
- **接口文档.md** — Backend API documentation (Chinese)

## Backend API (http://localhost:8086)

| Endpoint | Method | Purpose |
|---|---|---|
| `/v2/session/new` | POST | Create new session, returns `session_id` |
| `/v2/chat` | POST | Send question (with optional `session_id`), returns `reply_message` in Markdown |
| `/v2/session/list` | GET | List sessions with cursor pagination |
| `/v2/session/history` | GET | Get message history for a session |
| `/v2/rerank` | POST | Rerank documents by relevance |
| `/v2/rewrite` | POST | Rewrite ambiguous queries using conversation history |

## Key Patterns

- AI responses are Markdown rendered client-side via the `marked` library (loaded from CDN)
- Sessions are stored in Redis on the backend; session IDs are UUIDs
- Conversation state lives entirely in the `conversations` array in `script.js`
- History list items are prefixed with `chatHistory:` from the API and stripped client-side
- All async functions that create or fetch data must be `await`ed — forgetting `await` on `ensureConversation()` is a known bug pattern
