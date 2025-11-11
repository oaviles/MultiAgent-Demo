# Multi-Agent System with Microsoft Agent Framework on AKS

[![Deploy MCP Services to AKS](https://github.com/darkanita/MultiAgent-AKS-MAF/actions/workflows/deploy-mcp-to-aks.yml/badge.svg)](https://github.com/darkanita/MultiAgent-AKS-MAF/actions/workflows/deploy-mcp-to-aks.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A production-ready multi-agent orchestration system built with **Microsoft Agent Framework (MAF)**, **Agent-to-Agent (A2A) Protocol**, **Model Context Protocol (MCP)**, and **Azure Kubernetes Service (AKS)**.

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Orchestrator                              │
│              (A2A Protocol + Service Bus)                        │
└────────────┬────────────────────────────────┬───────────────────┘
             │                                │
             │ A2A Discovery                  │ A2A Discovery
             │                                │
   ┌─────────▼──────────┐         ┌──────────▼─────────────┐
   │   Travel Agent     │         │   External Agent       │
   │   (ChatAgent)      │         │   (A2A compliant)      │
   └─────────┬──────────┘         └────────────────────────┘
             │
             │ MCP Tools
             │
   ┌─────────▼──────────┬──────────────────────┐
   │                    │                      │
   │ Currency MCP       │  Activity MCP        │
   │ (Frankfurter API)  │  (Planning tools)    │
   └────────────────────┴──────────────────────┘
```

## 📁 Project Structure

```
MultiAgent-AKS-MAF/
├── agents/                      # MAF-based agents
│   ├── orchestrator/           # Main orchestrator (A2A + Service Bus)
│   │   ├── main.py
│   │   ├── requirements.txt
│   │   └── Dockerfile
│   │
│   ├── travel_agent/           # Travel planning agent
│   │   ├── main.py
│   │   ├── requirements.txt
│   │   ├── Dockerfile
│   │   └── .well-known/
│   │       └── agent.json      # AgentCard for A2A discovery
│   │
│   └── external_agent/         # External A2A agent integration
│       ├── README.md          # Integration guide
│       └── .well-known/
│           └── agent.json     # AgentCard template
│
├── mcp_servers/                # Model Context Protocol servers
│   ├── currency_mcp/          # Currency exchange tools
│   │   ├── server.py
│   │   ├── requirements.txt
│   │   └── Dockerfile
│   │
│   └── activity_mcp/          # Activity planning tools
│       ├── server.py
│       ├── requirements.txt
│       └── Dockerfile
│
├── infrastructure/             # Azure infrastructure (Bicep)
│   └── bicep/
│       ├── main-rg.bicep      # Main resource group deployment
│       └── modules/           # Individual Azure resources
│
├── k8s/                       # Kubernetes manifests
│   ├── orchestrator-deployment.yaml
│   ├── travel-agent-deployment.yaml
│   ├── currency-mcp-deployment.yaml
│   └── activity-mcp-deployment.yaml
│
├── scripts/                   # Deployment scripts
│   ├── deploy-infrastructure.sh
│   ├── build-and-push.sh
│   └── deploy-to-aks.sh
│
└── _archived/                 # Old code (for reference)
```

## 🚀 Key Technologies

- **Microsoft Agent Framework (MAF)**: Agent orchestration and communication
- **Azure AI Foundry**: GPT-4o, GPT-4o-mini models
- **A2A Protocol**: Agent-to-Agent communication standard
- **MCP (Model Context Protocol)**: Tool/plugin architecture
- **Azure Service Bus**: Message queue for external communication
- **Azure Kubernetes Service (AKS)**: Container orchestration
- **Azure Managed Identity**: Secure authentication

## ✨ Features

### Orchestrator
- ✅ Discovers agents via **A2A AgentCard** resolution
- ✅ Receives tasks from **Azure Service Bus**
- ✅ Delegates to specialist agents based on capabilities
- ✅ Supports external A2A-compliant agents

### Travel Agent
- ✅ Built with **ChatAgent** from MAF
- ✅ Uses **MCP tools** for currency and activity planning
- ✅ Exposes **AgentCard** at `/.well-known/agent.json`
- ✅ Supports **Azure Managed Identity**

### MCP Servers
- ✅ **Currency MCP**: Exchange rates via Frankfurter API
- ✅ **Activity MCP**: Trip planning, recommendations

## 🔧 Prerequisites

- Azure subscription
- Azure CLI (`az`)
- kubectl
- Docker
- Python 3.11+

## 📦 Quick Start

### 1. Deploy Infrastructure

```bash
# Login to Azure
az login

# Deploy infrastructure
./scripts/deploy-infrastructure.sh
```

This creates:
- Azure OpenAI (with gpt-4o, gpt-4o-mini, gpt-35-turbo)
- Azure Service Bus
- Azure Key Vault
- AKS Cluster with Workload Identity
- Azure Container Registry

### 2. Deploy to AKS

#### Option A: GitHub Actions (Recommended) 🚀

See [GitHub Actions Deployment Guide](docs/GITHUB_ACTIONS_DEPLOYMENT.md) for automated CI/CD setup.

Quick setup:
```bash
# Create service principal with federated credentials
# See .github/DEPLOYMENT_QUICKSTART.md for complete script

# Add GitHub Secrets (Settings → Secrets):
# - AZURE_CLIENT_ID
# - AZURE_TENANT_ID  
# - AZURE_SUBSCRIPTION_ID

# Push to main branch → auto-deploys!
git push origin main
```

#### Option B: Manual Deployment

```bash
# Build and deploy all services
./scripts/deploy-to-aks.sh
```

This will:
- Build Docker images for all 3 services
- Push to Azure Container Registry
- Deploy to AKS with Workload Identity
- Configure session affinity for MCP servers
- Wait for pods and get external IP

## 🧪 Testing Locally

### Test MCP Servers

```bash
# Start Currency MCP
cd mcp_servers/currency_mcp
python server.py

# Test in another terminal
curl http://localhost:8001/health
```

### Test Travel Agent

```bash
cd agents/travel_agent
python main.py

# Query the agent
curl -X POST http://localhost:8000/run \
  -H "Content-Type: application/json" \
  -d '{"query": "Convert 500 USD to EUR and plan a day in Paris"}'
```

## 🔐 Security

- **Managed Identity**: All Azure resources use managed identities
- **RBAC**: Service Bus and Key Vault use role-based access
- **No API Keys**: Credentials stored in Key Vault
- **Network Isolation**: Private endpoints for Azure services

## 📊 Monitoring

- **Application Insights**: Telemetry and logging
- **Azure Monitor**: Infrastructure metrics
- **Service Bus Metrics**: Message queue monitoring

## 🛠️ Development

### Adding a New Agent

1. Create agent directory: `agents/my_agent/`
2. Implement using MAF `ChatAgent`
3. Create `agent.json` AgentCard
4. Build Docker image
5. Create K8s deployment manifest

### Adding MCP Tools

1. Create MCP server: `mcp_servers/my_tools/`
2. Implement tools following MCP spec
3. Register with Travel Agent
4. Deploy to AKS

## 📚 Resources

- [Microsoft Agent Framework](https://github.com/microsoft/agent-framework)
- [A2A Protocol](https://a2a-protocol.org/)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Azure AI Foundry](https://learn.microsoft.com/azure/ai-foundry/)

## 📝 License

MIT License - see LICENSE file for details

## 🤝 Contributing

Contributions welcome! Please read CONTRIBUTING.md first.

---

**Built with ❤️ using Microsoft Agent Framework**
