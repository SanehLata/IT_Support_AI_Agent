# 🤖 Enterprise IT Helpdesk Agent — AWS Bedrock AgentCore

A production-grade agentic AI system that autonomously handles enterprise IT helpdesk queries using **Amazon Bedrock AgentCore**, **LangGraph**, and **RAG (Retrieval-Augmented Generation)**. Built on real enterprise IT support scenarios drawn from global banking and financial services environments.

The agent progressively evolves across three implementations — from a basic LangGraph RAG agent to a fully deployed, memory-enabled agentic system on AWS Bedrock AgentCore — demonstrating enterprise-ready AI deployment patterns.

---

## 🏗️ Architecture Overview

```
User Query
    │
    ▼
LangGraph Agent (Tool Orchestration)
    │
    ├──► search_faq()           → FAISS Vector Store (HuggingFace Embeddings for local prototyping, Amazon Bedrock embeddings for production deployment)
    ├──► search_detailed_faq()  → Extended semantic search
    └──► reformulate_query()    → Aspect-focused query rewriting
    │
    ▼
LLM (Groq) → Synthesised Answer
    │
    ▼
AWS Bedrock AgentCore Runtime (Production Deployment)
    │
    └──► AgentCore Memory (Session + Long-term User Preference Store)
```

---

## 📚 Project Structure — Three Progressive Implementations

### `00_langgraph_agent.py` — Base LangGraph RAG Agent
A foundational LangGraph agent with semantic FAQ search capabilities.

**Key features:**
- RAG pipeline over an enterprise IT helpdesk knowledge base
- FAISS vector store with HuggingFace sentence embeddings
- Three specialised tools: standard search, detailed search, and query reformulation
- Multi-step agentic reasoning to synthesise answers from multiple sources

### `01_agentcore_runtime.py` — AWS Bedrock AgentCore Deployment
The same agent deployed to **Amazon Bedrock AgentCore** as a managed, scalable production service.

**Key features:**
- `BedrockAgentCoreApp` entrypoint for production deployment
- Tool definitions exposed via AgentCore runtime
- Query reformulation for complex, multi-aspect IT queries
- REST API invocation via `agentcore invoke`
- Production deployment with `agentcore launch`

### `02_agentcore_memory.py` — AgentCore with Persistent Memory
Advanced implementation adding **session continuity** and **long-term user preference memory**.

**Key features:**
- `AgentCoreMemorySaver` for session-level conversation checkpointing
- `AgentCoreMemoryStore` for long-term, cross-session user preference retrieval
- Pre-model hook: retrieves relevant user history before each LLM call
- Post-model hook: saves AI responses to persistent memory
- Actor ID and Thread ID based memory namespacing
- Personalised responses based on user history

---

## 💡 Enterprise Use Cases Covered

The IT helpdesk knowledge base covers **49 real enterprise scenarios** across:

| Category | Examples |
|---|---|
| Access & Identity | AD password resets, MFA, VPN, JML process, privileged access |
| Trading Systems | Repo platform access, Bloomberg terminal, market data |
| Security | Phishing response, lost devices, suspicious activity, certificate errors |
| Infrastructure | AutoSys jobs, Splunk, CI/CD pipelines, firewall rules, server decommission |
| DevOps & Change | Change management (CAB), production deployments, service accounts |
| Collaboration | Jira, Confluence, SharePoint, Teams, Outlook, OneDrive |
| Hardware & Support | Laptop issues, ServiceNow escalation, incident management (P1/P2) |

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Agent Orchestration | LangGraph |
| Cloud Deployment | Amazon Bedrock AgentCore |
| Memory | AgentCoreMemorySaver, AgentCoreMemoryStore |
| LLM | Groq (LLaMA / GPT-OSS) |
| Embeddings | HuggingFace — sentence-transformers/all-MiniLM-L6-v2(Test) | Amazon Bedrock Embeddings (Production)
| Vector Store | FAISS |
| RAG Framework | LangChain |
| Language | Python 3.13 |
| Package Manager | uv |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.13+
- AWS account with Amazon Bedrock AgentCore access
- AWS credentials configured (`aws configure`)
- Groq API key ([console.groq.com](https://console.groq.com))
- uv package manager

```bash
pip install uv
```

### Installation

```bash
# Clone the repository
git clone https://github.com/SanehLata/IT_Support_AI_Agent 
cd IT_Support_AI_Agent 

# Install dependencies
uv sync
```

### Configuration

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
HF_API_KEY=your_huggingface_api_key_here
```

---

## ▶️ Running the Agent

### Implementation 1 — Local LangGraph Agent

```bash
python 00_langgraph_agent.py
```

Tests the base agent with a sample query. Modify the query in `__main__` to test different helpdesk scenarios.

### Implementation 2 — Deploy to AWS Bedrock AgentCore

```bash
# Configure the AgentCore deployment
agentcore configure -e 01_agentcore_runtime.py

# Deploy to AWS Bedrock AgentCore
agentcore launch --env GROQ_API_KEY=your_groq_api_key_here

# Invoke the deployed agent
agentcore invoke '{"prompt": "My VPN is not connecting after the latest update. What should I do?"}'
```

### Implementation 3 — Deploy with Persistent Memory

```bash
# Configure with memory support
agentcore configure -e 02_agentcore_memory.py

# Deploy
agentcore launch --env GROQ_API_KEY=your_groq_api_key_here

# Invoke with session context
agentcore invoke '{
  "prompt": "How do I reset my AD password?",
  "actor_id": "employee-12345",
  "session_id": "session-abc"
}'
```

---

## 🧪 Sample Queries to Test

```bash
# Access Management
agentcore invoke '{"prompt": "How do I request access to a new trading system?"}'

# Security Incident
agentcore invoke '{"prompt": "I received a suspicious phishing email. What should I do?"}'

# Production Issue
agentcore invoke '{"prompt": "My AutoSys job failed overnight. How do I investigate and rerun?"}'

# Trading Platform
agentcore invoke '{"prompt": "My Bloomberg terminal is not launching. What are the steps to resolve this?"}'

# P1 Incident
agentcore invoke '{"prompt": "What is the process for reporting a system outage affecting trading?"}'
```

---

## 🏛️ Key Design Patterns

### Multi-Tool Agentic Reasoning
The agent selects between three tools based on query complexity — starting with a standard search, escalating to detailed retrieval, and using query reformulation for multi-faceted questions. This mirrors enterprise-grade agentic patterns used in production AI systems.

### RAG Pipeline
Queries are embedded using HuggingFace sentence transformers, matched against a FAISS index of the helpdesk knowledge base, and top-k results are synthesised by the LLM into a coherent, actionable response.

### Production Deployment via AgentCore
The `@app.entrypoint` decorator exposes the agent as a managed AWS service — handling authentication, scaling, and invocation routing automatically. This eliminates the need to manage LLM infrastructure directly.

### Persistent Memory Architecture
The memory implementation uses two layers:
- **Session memory** (`AgentCoreMemorySaver`): maintains conversation context within a session
- **Long-term memory** (`AgentCoreMemoryStore`): retrieves user preferences and history across sessions using semantic search

---

## 📊 Comparison of Implementations

| Feature | 00 Basic | 01 AgentCore | 02 + Memory |
|---|:---:|:---:|:---:|
| LangGraph agent | ✅ | ✅ | ✅ |
| RAG pipeline | ✅ | ✅ | ✅ |
| AWS cloud deployment | ❌ | ✅ | ✅ |
| REST API invocation | ❌ | ✅ | ✅ |
| Session memory | ❌ | ❌ | ✅ |
| Long-term user memory | ❌ | ❌ | ✅ |
| Personalised responses | ❌ | ❌ | ✅ |

---

## 🔗 Related Projects

- [AI Onboarding Buddy — LangGraph Agentic System](https://github.com/SanehLata/Onboarding_AI_Buddy)
- [Web Intel AI — RAG Application](https://github.com/SanehLata/Web_Intel_AI_RAG)
- [ShopAssist — GenAI E-Commerce Chatbot](https://github.com/SanehLata/ShopAssist_GenAIChatbot)

---

## 👤 Author

**Sneh Dehmiwal** — AI/ML Engineer & Technical Delivery Leader  
📍 Chicago, IL | [LinkedIn](https://www.linkedin.com/in/snehdehmiwal/) | [Portfolio](https://sanehlata.github.io) | [GitHub](https://github.com/SanehLata)

---

## 📄 Learnings from the Project
when I launched Bedrock Agentcore - 

    agentcore launch --env GROQ_API_KEY=gsk_your_api_key
                    
it failed with error :

    CodeBuild completed successfully 
    CodeBuild project configuration saved    
    Deploying to Bedrock AgentCore... ❌ Launch failed: An error occurred (ServiceQuotaExceededException) when calling the CreateAgentRuntime operation: maxImageSizeMb limit exceeded for account 075452627099. Please contact AWS Support for more information.
    
This is because container image (built via CodeBuild) was too large for AWS Bedrock AgentCore limits. I replaced HuggingFaceEmbeddings ❌ (pulls PyTorch + Transformers (~1–3GB)) and embedding model:"sentence-transformers/all-MiniLM-L6-v2" with AWS Bedrock embeddings.

So, I initially used HuggingFace embeddings for local prototyping, but for production deployment on AWS AgentCore, I switched to API-based embeddings to reduce container size, improve scalability, and align with cloud-native architecture.

