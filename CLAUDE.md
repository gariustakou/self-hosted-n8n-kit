# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **n8n Self-hosted AI Starter Kit** - a Docker Compose template designed for rapid setup of a local AI and low-code development environment. The kit combines n8n with AI components (Ollama, Qdrant, PostgreSQL) to enable building self-hosted AI workflows.

### Core Architecture

- **n8n**: Low-code workflow automation platform (port 5678)
- **Open WebUI**: Modern web interface for AI model interaction (port 3000)
- **Ollama**: Local LLM inference engine (port 11434) 
- **Qdrant**: Vector database for embeddings (port 6333)
- **PostgreSQL**: Primary database for n8n data
- **Docker Compose**: Orchestrates all services with networking and volumes

### Key Components

- `docker-compose.yml`: Main service definitions with GPU/CPU profiles
- `shared/`: Volume mount for local file access in workflows (maps to `/data/shared` in n8n, `/app/backend/data/uploads` in Open WebUI)
- `.env`: Environment configuration (copy from `.env.example`)

## Common Commands

### Starting the Stack

For CPU-only deployment:
```bash
docker compose --profile cpu up
```

For Nvidia GPU:
```bash
docker compose --profile gpu-nvidia up
```

For AMD GPU (Linux):
```bash
docker compose --profile gpu-amd up
```

For Mac with local Ollama:
```bash
docker compose up
```

### Management Commands

```bash
# Pull latest images and recreate
docker compose --profile [cpu|gpu-nvidia|gpu-amd] pull
docker compose create && docker compose --profile [cpu|gpu-nvidia|gpu-amd] up

# Stop services
docker compose down

# View logs
docker compose logs -f [service-name]

# Access n8n shell
docker compose exec n8n /bin/sh
```

### Setup Requirements

1. Copy environment template: `cp .env.example .env`
2. Update secrets in `.env` file (especially encryption keys)
3. For Mac users with local Ollama, set `OLLAMA_HOST=host.docker.internal:11434` in `.env`
4. Configure Open WebUI authentication: Set `WEBUI_AUTH=False` for single-user mode

### Access Points

- **n8n**: http://localhost:5678 - Workflow automation interface
- **Open WebUI**: http://localhost:3000 - AI chat interface (first user becomes admin)
- **Qdrant**: http://localhost:6333 - Vector database API
- **Ollama**: http://localhost:11434 - LLM inference API

## Development Guidelines

### File System Access

Both n8n and Open WebUI can access local files through shared volume mounts:
- **n8n**: Use `/data/shared` path for local files in workflow nodes (Read/Write Files, Local File Trigger, Execute Command)
- **Open WebUI**: Upload files accessible at `/app/backend/data/uploads` for document processing and RAG

Both paths map to the `./shared` directory in the project root.

### Environment Configuration

The stack uses Cloudflare-specific webhook URLs and HTTPS configuration. Key environment variables:
- `WEBHOOK_URL=https://n8n.gariusdeveloper.com` 
- `N8N_EDITOR_BASE_URL=https://n8n.gariusdeveloper.com`
- `N8N_SECURE_COOKIE=true`

### AI Interaction Options

The stack provides two complementary interfaces for AI interaction:

**Open WebUI** (Recommended for direct model testing):
- Modern chat interface with conversation history
- Support for file uploads and RAG capabilities  
- Model management and configuration
- Multi-user support with role-based access

**n8n workflows**:
- Automated workflow integration with AI models
- Complex logic and data processing pipelines
- Integration with external systems and APIs

### Vision and Scope

This is a **learning and experimentation platform**, not production-ready infrastructure. Focus is on simplicity and quick setup rather than comprehensive features. The stack prioritizes:
- Privacy-first local development
- Minimal external dependencies
- Fast path from zero to working AI workflows

Avoid adding production infrastructure (reverse proxies, advanced monitoring, enterprise features) per the contribution guidelines.