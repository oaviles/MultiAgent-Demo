# Multi-Agent System on AKS with Microsoft Agent Framework

A production-ready multi-agent system deployed on Azure Kubernetes Service (AKS) using:
- **Azure AI Foundry** for LLM models
- **Microsoft Agent Framework** for orchestration
- **Model Context Protocol (MCP)** for agent capabilities
- **Agent-to-Agent (A2A)** communication
- **Web UI** for testing and interaction

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         Web UI                              │
│                  (Streamlit/React + FastAPI)                │
└─────────────────────────┬───────────────────────────────────┘
                          │
┌─────────────────────────┴───────────────────────────────────┐
│                   Orchestrator Agent                        │
│              (Microsoft Agent Framework)                    │
└─────┬───────────────────┬───────────────────────┬───────────┘
      │                   │                       │
┌─────┴─────┐    ┌────────┴────────┐    ┌────────┴─────────┐
│  Agent 1  │    │    Agent 2      │    │    Agent 3       │
│  (Data)   │    │    (Code)       │    │   (Research)     │
└─────┬─────┘    └────────┬────────┘    └────────┬─────────┘
      │                   │                       │
      └───────────────────┴───────────────────────┘
                          │
                    ┌─────┴─────┐
                    │ MCP Tools │
                    └───────────┘
                          │
                 ┌────────┴─────────┐
                 │  Azure AI Foundry │
                 │   (LLM Models)    │
                 └───────────────────┘
```

## Infrastructure

- **AKS Cluster**: Kubernetes orchestration
- **Azure AI Foundry**: GPT-4, Claude, and custom models
- **Azure Service Bus**: Agent-to-Agent messaging
- **Azure Container Registry**: Container images
- **Application Insights**: Monitoring and observability
- **Azure Key Vault**: Secrets management

## Quick Start

### Prerequisites
- Azure subscription
- Azure CLI installed
- kubectl installed
- Docker installed
- Python 3.11+

### Deploy Infrastructure

```bash
# Login to Azure
az login

# Deploy infrastructure using Bicep
cd infrastructure/bicep
az deployment sub create \
  --location eastus \
  --template-file main.bicep \
  --parameters main.parameters.json
```

### Deploy Application

```bash
# Build and push containers
./scripts/build-and-push.sh

# Deploy to AKS
kubectl apply -f k8s/
```

## Project Structure

```
MultiAgent-AKS-MAF/
├── infrastructure/          # Infrastructure as Code (Bicep)
├── agents/                  # Agent implementations
├── mcp_servers/            # MCP server implementations
├── web_ui/                 # Web UI (FastAPI + Streamlit)
├── communication/          # A2A communication layer
├── k8s/                    # Kubernetes manifests
├── .github/workflows/      # CI/CD pipelines
└── docs/                   # Documentation
```

## Development

See [DEVELOPMENT.md](docs/DEVELOPMENT.md) for local development setup.

## License

MIT
