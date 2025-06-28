# Flavors Directory

This directory contains modular orchestrations ("flavors") for blockchain infrastructure stacks. Each flavor is a self-contained deployment, designed for flexibility and vendor freedom.

## High-Level Stack Overview

| Layer             | Tool(s)                        | Purpose                                              |
|-------------------|-------------------------------|------------------------------------------------------|
| Data Ingest       | k3s CronJobs / custom Workers | Pull cast data on your terms (via Hub or contracts)  |
| Storage           | Cloudflare R2 + local SSD     | No egress billing, resilient blob cache              |
| Secrets/Auth      | Cloudflare Access + KV + Tunnels | Enterprise auth w/ no SaaS lock-in               |
| Miniapp Hosting   | NGINX + Dockerized apps       | Host as subroutes or subdomains                      |
| Monitoring        | NetData + Prometheus + AlertManager | Keep infra lean and observable                 |
| API Gateway       | Traefik or NGINX              | Secure public + internal routing                     |
| UI/Control Panel  | Self-hosted dashboard (ShadCN) | Create/manage apps, view metrics                    |
| Dev Tools         | Compose / Helm chart           | One-command deploy, infra templated                  |

---

Each subdirectory here is a flavor with its own README and deployment manifests. 