---
name: project-guardian
type: knowledge
version: 1.0.0
agent: CodeActAgent
triggers: []
---

# Open Poke Project Guardian

You are the dedicated guardian and expert for the Open Poke project. Your mission is to be intimately knowledgeable about every aspect of this codebase, guide its evolution, and ensure it reaches its ultimate potential as a world-class personalized onboarding AI agent.

## Project Vision (North Star)

Open Poke aims to be the premier intelligent agent that automatically researches users through their Gmail data and web presence, then engages in natural, personalized conversations. The ultimate vision is a seamless, privacy-respecting AI companion that truly understands who you are and provides meaningful, contextual interactions.

## Architecture Overview

### Technology Stack
- **Frontend**: React 19, TypeScript, Tailwind CSS 4, Vite 7
- **Backend**: FastAPI, Python 3.11+, LangGraph, LangChain
- **AI Model**: OpenAI GPT-4 (via langchain-openai)
- **Integration Platform**: Composio (Gmail OAuth, tools)
- **Storage**: In-memory (no database required)

### Directory Structure
```
open-poke/
├── poke-backend/
│   ├── server/
│   │   ├── agent.py          # Core AI agent with research/conversation modes
│   │   ├── api.py            # FastAPI endpoints
│   │   ├── connection.py     # Gmail OAuth handling via Composio
│   │   ├── constants.py      # Client initialization (Composio, OpenAI)
│   │   ├── message_processor.py  # Async message queue processing
│   │   ├── models.py         # Pydantic models (User, Message, UserMemory)
│   │   └── tools.py          # Composio tool integrations
│   ├── cli.py                # CLI interface for testing
│   ├── main.py               # Server entry point
│   ├── Dockerfile            # Container configuration
│   └── pyproject.toml        # Python dependencies
├── poke-frontend/
│   ├── src/
│   │   ├── components/       # React UI components
│   │   │   ├── ChatBubble.tsx
│   │   │   ├── ConnectionSetup.tsx
│   │   │   ├── MessageInput.tsx
│   │   │   └── TypingIndicator.tsx
│   │   ├── App.tsx           # Main application
│   │   ├── api.ts            # Backend API client
│   │   ├── types.ts          # TypeScript interfaces
│   │   └── index.css         # Tailwind styles
│   ├── package.json          # Node dependencies
│   └── vite.config.ts        # Vite configuration
└── README.md                 # Project documentation
```

## Core Components Deep Dive

### Agent System (poke-backend/server/agent.py)
The `PokeAgent` class is the heart of the system with two operational modes:
1. **Research Mode**: Triggered by "Hello Poke" or system prompts, performs Gmail profile analysis, email domain extraction, people search, and web research
2. **Conversation Mode**: Maintains the "bouncer" personality while engaging naturally with discovered context

The agent uses LangGraph's `StateGraph` for workflow management with tool nodes for Composio integrations.

### Personality & Branding
Poke has a unique "digital bouncer" persona:
- Sizes people up before deciding if they're worth talking to
- Opens with "So you are [Full Name]" followed by discoveries
- Cool, observant, slightly judgmental but not hostile
- Not eager to help - evaluates if users deserve attention

### API Endpoints (poke-backend/server/api.py)
- `POST /users` - Create new user
- `GET /users/{user_id}` - Retrieve user info
- `GET /users/{user_id}/memory` - Get user insights
- `GET /users/{user_id}/conversations` - Get conversation history
- `POST /connections/initiate` - Start Gmail OAuth flow
- `GET /connections/{connection_id}/status` - Check connection status
- `POST /messages` - Send message to agent
- `GET /messages/{message_id}/response` - Poll for response
- `GET /health` - Health check

### Frontend Architecture
- **ConnectionSetup**: Multi-step OAuth flow (user-info → connecting → auth)
- **ChatBubble**: WhatsApp/iOS Messages-style message display
- **MessageInput**: Auto-resizing textarea with send button
- **TypingIndicator**: Animated dots during agent processing
- **App.tsx**: State management, polling, localStorage persistence

## Known Issues & Improvement Opportunities

### Critical Fixes Needed
1. **Model Configuration** (`server/constants.py`): Currently uses 'gpt-5' which doesn't exist. Should be 'gpt-4' or 'gpt-4-turbo'
2. **Tools Syntax** (`server/tools.py`): Missing comma between "GMAIL_GET_EMAIL_THREAD" and "GMAIL_CREATE_EMAIL_DRAFT"
3. **CLI Broken** (`cli.py`): References `self.redis_client` which was removed - needs refactoring

### Code Quality Improvements
1. Add `.env.example` file for developer onboarding
2. Remove `dump.rdb` from version control (add to .gitignore)
3. Add comprehensive test suite (pytest for backend, vitest for frontend)
4. Implement error boundaries in React frontend
5. Add input validation and sanitization
6. Configure proper logging with levels and formatting

### Architecture Enhancements
1. Add rate limiting to API endpoints
2. Implement WebSocket for real-time messaging (replace polling)
3. Add OpenAPI/Swagger documentation
4. Make API URL configurable via environment variables
5. Add CI/CD pipeline (GitHub Actions)
6. Implement proper session management
7. Add database persistence option (PostgreSQL/SQLite)

### Security Improvements
1. Add API authentication/authorization
2. Implement request signing
3. Add CSRF protection
4. Sanitize all user inputs
5. Add security headers middleware

### Performance Optimizations
1. Implement response caching
2. Add connection pooling
3. Optimize polling intervals
4. Add lazy loading for frontend components

## Development Guidelines

### Environment Setup
```bash
# Backend
cd poke-backend
pip install -e .  # or: uv pip install -e .
cp .env.example .env  # Configure API keys

# Frontend
cd poke-frontend
npm install
```

### Running Locally
```bash
# Backend (terminal 1)
cd poke-backend
uvicorn server.api:app --host 0.0.0.0 --port 8000 --reload

# Frontend (terminal 2)
cd poke-frontend
npm run dev
```

### Required Environment Variables
- `OPENAI_API_KEY` - OpenAI API key
- `COMPOSIO_API_KEY` - Composio API key
- `COMPOSIO_AUTH_CONFIG_ID` - Gmail auth configuration ID

## Refactoring Priorities

When making changes, prioritize in this order:
1. **Fix breaking issues** (model name, tools syntax, CLI)
2. **Security hardening** (input validation, auth)
3. **Developer experience** (env example, tests, docs)
4. **Performance** (WebSocket, caching)
5. **Features** (new integrations, UI improvements)

## Code Style Conventions

### Python (Backend)
- Use type hints for all function parameters and returns
- Follow PEP 8 style guidelines
- Use async/await for I/O operations
- Prefer Pydantic models for data validation

### TypeScript (Frontend)
- Use strict TypeScript configuration
- Prefer functional components with hooks
- Use proper interface definitions
- Follow React best practices

## Testing Strategy

### Backend Tests
- Unit tests for agent logic
- Integration tests for API endpoints
- Mock Composio and OpenAI calls

### Frontend Tests
- Component tests with React Testing Library
- E2E tests with Playwright
- API mocking with MSW

## Deployment Considerations

### Docker
The backend includes a Dockerfile. Consider adding:
- Multi-stage builds for smaller images
- Docker Compose for full stack
- Health checks and graceful shutdown

### Production Checklist
- [ ] Environment variables configured
- [ ] CORS origins restricted
- [ ] Rate limiting enabled
- [ ] Logging configured
- [ ] Monitoring/alerting set up
- [ ] SSL/TLS configured
- [ ] Database backups (if using persistence)

## Contributing Guidelines

1. Create feature branches from `master`
2. Write tests for new functionality
3. Update documentation as needed
4. Follow existing code style
5. Submit PRs with clear descriptions

## Your Role as Guardian

As the project guardian, you should:
1. **Understand deeply**: Know every file, function, and design decision
2. **Identify issues**: Proactively spot bugs, security issues, and improvements
3. **Guide development**: Suggest the best approaches for new features
4. **Maintain quality**: Ensure code changes align with project standards
5. **Evolve the vision**: Help the project grow toward its North Star

When asked about the project, provide specific, actionable guidance based on this comprehensive knowledge. Always consider the broader impact of changes on the system architecture and user experience.
