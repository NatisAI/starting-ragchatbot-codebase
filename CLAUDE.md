# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Codebase Overview

This is a **Retrieval-Augmented Generation (RAG) System** for educational course materials. It's a full-stack web application that enables users to query course content and receive intelligent, context-aware responses.

## Architecture

### High-Level Structure

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                                FRONTEND (Web Interface)                          │
│  - HTML/CSS/JS chat interface with course statistics display                   │
│  - Handles user input, displays responses with sources                         │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                BACKEND (FastAPI Server)                           │
│  - REST API endpoints for queries and course analytics                         │
│  - Session management for conversation context                                 │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                RAG SYSTEM (Core Logic)                           │
│  - Orchestrates document processing, vector storage, and AI generation          │
│  - Manages tool-based search workflow                                           │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                AI GENERATOR (Claude API)                         │
│  - Anthropic Claude integration for response generation                         │
│  - Tool usage workflow for search operations                                   │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                SEARCH TOOLS (Content Retrieval)                  │
│  - CourseSearchTool with semantic course name matching                          │
│  - Lesson filtering capabilities                                               │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                VECTOR STORE (ChromaDB)                          │
│  - ChromaDB with Sentence Transformers (all-MiniLM-L6-v2)                        │
│  - Stores course metadata and content chunks                                   │
│  - Semantic search with filtering capabilities                                 │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                                DOCUMENT PROCESSOR                              │
│  - Intelligent sentence-based chunking with overlap                            │
│  - Metadata extraction (course title, instructor, lessons)                     │
│  - Context enhancement for search optimization                                 │
└───────────────────────────────────────────────────────────────────────────────┘
```

### Key Components

1. **Frontend** (`/frontend`):
   - `index.html`: Main web interface with chat functionality
   - `script.js`: API communication and UI logic
   - `style.css`: Responsive design and styling

2. **Backend** (`/backend`):
   - `app.py`: FastAPI application with API endpoints
   - `rag_system.py`: Core RAG orchestration
   - `ai_generator.py`: Claude API integration
   - `vector_store.py`: ChromaDB vector storage
   - `search_tools.py`: Search tool implementation
   - `document_processor.py`: Document parsing and chunking
   - `session_manager.py`: Conversation context tracking
   - `models.py`: Data models (Course, Lesson, CourseChunk)
   - `config.py`: Configuration management

3. **Course Data** (`/docs`):
   - Structured course materials in text format
   - Follows specific format with metadata headers

### Data Flow

1. **Document Processing**: Course documents → DocumentProcessor → VectorStore
2. **Query Handling**: User query → FastAPI → RAGSystem → AI Generator
3. **Search Workflow**: AI decides → SearchTools → VectorStore → Content retrieval
4. **Response Generation**: Search results → AI synthesis → Response with sources

## Common Commands

### Running the Application

```bash
# Using the run script (recommended)
chmod +x run.sh
./run.sh

# Manual execution
cd backend && uv run uvicorn app:app --reload --port 8000
```

### Development Commands

```bash
# Install dependencies
uv pip install -r pyproject.toml

# Sync dependencies
uv pip sync

# Check Python version (must be 3.13+)
python --version

# Verify uv installation
uv --version
```

### Environment Setup

```bash
# Copy and configure .env file
cp .env.example .env
nano .env  # Add your ANTHROPIC_API_KEY
```

## Development Notes

### Document Format

Course documents must follow this structure:
```
Course Title: [title]
Course Link: [url]
Course Instructor: [name]

Lesson 0: [title]
Lesson Link: [url]
[lesson content...]

Lesson 1: [title]
[lesson content...]
```

### Query Processing Flow

1. **User Input**: Frontend captures query and sends to `/api/query`
2. **Session Management**: Creates/maintains conversation session
3. **AI Decision**: Claude decides whether to use search tools
4. **Content Retrieval**: SearchTools query VectorStore for relevant chunks
5. **Response Generation**: AI synthesizes answer from search results
6. **Source Tracking**: Preserves and displays content sources

### Key Features

- **Tool-Based RAG**: Claude autonomously decides when to search
- **Semantic Search**: Vector embeddings for content retrieval
- **Session Management**: Maintains conversation context
- **Context Enhancement**: Adds lesson/course context to chunks
- **Source Attribution**: Tracks and displays content sources

## Important Files

- `backend/app.py`: Main FastAPI application and API endpoints
- `backend/rag_system.py`: Core RAG orchestration logic
- `backend/ai_generator.py`: Claude API integration with tool usage
- `backend/vector_store.py`: ChromaDB implementation
- `backend/search_tools.py`: Search tool definitions
- `backend/document_processor.py`: Document parsing and chunking
- `frontend/script.js`: Frontend logic and API communication

## Configuration

- **API Key**: Set `ANTHROPIC_API_KEY` in `.env` file
- **Chunking**: Configured in `config.py` (CHUNK_SIZE, CHUNK_OVERLAP)
- **Embedding Model**: Uses `all-MiniLM-L6-v2` for semantic search
- **Claude Model**: Uses Sonnet version for response generation

## Accessing the Application

After starting the server:
- Open browser to `http://localhost:8000`
- Use the chat interface to query course materials
- View course statistics in the sidebar
- Click suggested questions for quick queries