# Farcaster Backend (flavor)

This directory contains the DebOps orchestration for a self-hosted, vendor-free Farcaster backend stack.

## Overview
- Kubernetes manifests are provided in the `k8s/` directory for deployment.
- Designed for easy deployment on K3s or any Kubernetes cluster.

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

## Contents
- `k8s/`: Kubernetes manifests for deployment (Ingress, Deployment, Service).

---

For more information, see the root README. 