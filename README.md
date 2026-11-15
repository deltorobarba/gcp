# Generative AI on Google Cloud

## Notebooks

* [Agent](https://github.com/deltorobarba/gcp/blob/main/code/agents.ipynb): Pipeline with ADK workflow graph, Memory Bank, Agent Engine deployment, agent evaluation
* [MCP](https://github.com/deltorobarba/gcp/blob/main/code/mcp.ipynb): on Cloud Run & Agent Platform. BigQuery MCP server, OIDC, ADK MCPToolset, Managed MCP
* [Models](https://github.com/deltorobarba/gcp/blob/main/code/models.ipynb): Gemini, Gemma and open models via GenAI SDK, OpenAI SDK, latency benchmark (TTFT, P50–P99)
* [Identity](https://github.com/deltorobarba/gcp/blob/main/code/identity.ipynb): SPIFFE-based Agent Identity, Agent Registry, IAM, Agent Runtime, least-privilege BigQuery tool
* [Gateway](https://github.com/deltorobarba/gcp/blob/main/code/gateway.ipynb): Agent Gateway, ingress (CLIENT_TO_AGENT) vs. egress, Model Armor, IAP, binding at create time
* [A2A](https://github.com/deltorobarba/gcp/blob/main/code/a2a.ipynb): Agent-to-Agent 1.0, Agent Card, A2aAgent server, a2a-sdk client, Agent Identity, mTLS, custom IAM role
* [Github](https://github.com/deltorobarba/gcp/blob/main/code/github.ipynb): GitHub MCP server, Secret Manager, tool filter, prompt injection, Auth Manager

## Learnings

Lernnotizen mit Erklärungen und Mermaid-Grafiken im Ordner [learnings/](https://github.com/deltorobarba/gcp/tree/main/learnings).

**Agents und generative KI**

* [SDLC](https://github.com/deltorobarba/gcp/blob/main/learnings/sdlc.md): AI for Agent authentication and authorization, access vs. ID tokens, ADC, SDLC agenda
* [Authentification](https://github.com/deltorobarba/gcp/blob/main/learnings/authentification.md): Bausteine (Agent, Runtime, MCP, A2A, Identity), OIDC vs. Access-Token, Registry header_provider, Fehlerbilder 401/403, Google-managed OAuth
* [Skills](https://github.com/deltorobarba/gcp/blob/main/learnings/skill.md): Agent Skills at scale, SKILL.md, Agent Registry lifecycle and revisions, 3-stage governance, rules vs. skills vs. MCP vs. hooks, top-k skill retrieval, domain subagents
* [Evaluations](https://github.com/deltorobarba/gcp/blob/main/learnings/evaluations.md): LLM- und Agent-Leaderboards, Modell vs. Harness, pass@k vs. pass^k, LLM-Judge kalibrieren

**Google Cloud Architecture (Professional Cloud Architect)**

* [GKE](https://github.com/deltorobarba/gcp/blob/main/learnings/gke.md): Control Plane und Node Pools, Standard vs. Autopilot, Deployment vs. StatefulSet, Services und Ingress, private Nodes, HPA und Cluster Autoscaler, Rollouts und Probes, Workload Identity, Binary Authorization, Fleet
* [VPC](https://github.com/deltorobarba/gcp/blob/main/learnings/vpc.md): globales VPC und regionale Subnetze, Firewall-Regeln (Prioritäten, Tags vs. Service Accounts), Shared VPC vs. Peering, Private Google Access, Cloud NAT, Private Services Access vs. Private Service Connect, VPC Service Controls, HA VPN, Dedicated und Partner Interconnect
* [Load Balancing](https://github.com/deltorobarba/gcp/blob/main/learnings/loadbalancing.md): Application vs. Proxy vs. Passthrough Network LB, global vs. regional, URL Maps, Health Checks und Autohealing, Failover über Regionen, Cloud CDN und Cache Keys, Cloud Armor, IAP, Serverless NEGs für Cloud Run
