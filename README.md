# Generative AI on Google Cloud

* [Agent](https://github.com/deltorobarba/gcp/blob/main/agents.ipynb): Pipeline with ADK workflow graph, Memory Bank, Agent Engine deployment, agent evaluation
* [MCP](https://github.com/deltorobarba/gcp/blob/main/mcp.ipynb): on Cloud Run & Agent Platform. BigQuery MCP server, OIDC, ADK MCPToolset, Managed MCP
* [Models](https://github.com/deltorobarba/gcp/blob/main/models.ipynb): Gemini, Gemma and open models via GenAI SDK, OpenAI SDK, latency benchmark (TTFT, P50–P99)
* [Evaluations](https://github.com/deltorobarba/gcp/blob/main/evaluations.md): LLM- und Agent-Leaderboards, Modell vs. Harness, pass@k vs. pass^k, LLM-Judge kalibrieren
* [Identity](https://github.com/deltorobarba/gcp/blob/main/identity.ipynb): SPIFFE-based Agent Identity, Agent Registry, IAM, Agent Runtime, least-privilege BigQuery tool
* [Gateway](https://github.com/deltorobarba/gcp/blob/main/gateway.ipynb): Agent Gateway, ingress (CLIENT_TO_AGENT) vs. egress, Model Armor, IAP, binding at create time
* [A2A](https://github.com/deltorobarba/gcp/blob/main/a2a.ipynb): Agent-to-Agent 1.0, Agent Card, A2aAgent server, a2a-sdk client, Agent Identity, mTLS, custom IAM role
* [Github](https://github.com/deltorobarba/gcp/blob/main/github.ipynb): GitHub MCP server, Secret Manager, tool filter, prompt injection, Auth Manager
* [SDLC](https://github.com/deltorobarba/gcp/blob/main/sdlc.md): AI for Agent authentication and authorization, access vs. ID tokens, ADC, SDLC agenda
* [Skills](https://github.com/deltorobarba/gcp/blob/main/skill.md): Agent Skills at scale, SKILL.md, Agent Registry lifecycle and revisions, 3-stage governance, rules vs. skills vs. MCP vs. hooks, top-k skill retrieval, domain subagents
* [Authentification](https://github.com/deltorobarba/gcp/blob/main/authentification.md): Bausteine (Agent, Runtime, MCP, A2A, Identity), OIDC vs. Access-Token, Registry header_provider, Fehlerbilder 401/403, Google-managed OAuth
