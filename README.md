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

Ein Thema pro Datei. Kosten (Abrechnung, Rabatte, Spot, Datenverkehr) bekommen in jeder Notiz einen eigenen Abschnitt.

* [Compute und Serverless](https://github.com/deltorobarba/gcp/blob/main/learnings/compute.md): Wo läuft mein Code (Entscheidungsbaum), Maschinenfamilien, eigene Images und Instanzvorlagen, Managed Instance Groups (Autoscaling, Autohealing, Rolling Updates), Spot-VMs, Sole-Tenant-Nodes, VM Manager, GPUs, Cloud Run vs. Cloud Run Functions vs. App Engine
* [GKE](https://github.com/deltorobarba/gcp/blob/main/learnings/gke.md): Control Plane und Node Pools, Standard vs. Autopilot, Deployment vs. StatefulSet, Services und Ingress, private Nodes, HPA und Cluster Autoscaler, Rollouts und Probes, Workload Identity, Binary Authorization, Fleet
* [Networking](https://github.com/deltorobarba/gcp/blob/main/learnings/networking.md): L3/L4/L7-Schichtenmodell, globales VPC und regionale Subnetze, Firewall-Regeln (Prioritäten, Tags vs. Service Accounts), Shared VPC vs. Peering, Private Google Access, Cloud NAT, Private Services Access vs. Private Service Connect, VPC Service Controls, HA VPN, Dedicated und Partner Interconnect, Application vs. Proxy vs. Passthrough Network LB, URL Maps, Health Checks, Cloud CDN, Cloud Armor
* [IAM und Security](https://github.com/deltorobarba/gcp/blob/main/learnings/security.md): Ressourcenhierarchie, IAM-Rollen, Service Accounts, Organization Policies, Cloud KMS und CMEK, Secret Manager, Sensitive Data Protection, Compliance
* [Data](https://github.com/deltorobarba/gcp/blob/main/learnings/data.md): welcher Speicher wofür (Entscheidungsbaum), Cloud-Storage-Klassen und Lifecycle, Medien mit Cloud CDN ausliefern, Persistent Disk und Filestore, Datentransfer, Cloud SQL, AlloyDB, Spanner, Bigtable (Row Keys), Firestore, Memorystore, BigQuery, Pub/Sub, Dataflow, Dataproc
* [Operations](https://github.com/deltorobarba/gcp/blob/main/learnings/operations.md): Cloud Build, Cloud Deploy, Release-Strategien, Terraform, Cloud Monitoring und Logging, Trace und Profiler, SLOs und Error Budgets, RPO und RTO, DR-Muster, Backup and DR Service, Migration Center, Migrate to VMs, Database Migration Service
