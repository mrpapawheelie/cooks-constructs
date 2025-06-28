# Farcaster Backend (flavor)

This directory contains the DevOps orchestration for a self-hosted, vendor-free Farcaster backend stack.

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

---

## 🌐 Infra Topology (Recap)
- 3 Lightsail Servers (Ubuntu 22.04)
  - master-node → Control plane
  - worker-node-1 → App services
  - worker-node-2 → DB/cache/stateful services
- Static IPs → Attached to all 3
- Domain → Pointed to master (api.farcasterforge.xyz or similar)
- Key Sharing → SSH key shared among nodes

---

## 🔐 Key + Secret Management

| Method                | Use                                              |
|-----------------------|-------------------------------------------------|
| GitHub Actions Secrets| For encrypted access tokens, API keys (CF token, etc.) |
| Cloudflare KV or R2   | Store rotated secrets like JWT signing keys      |
| On-server SSH sharing | Add master's pubkey to each worker's ~/.ssh/authorized_keys |
| Env Vaulting          | Mount .env at runtime via GitHub Actions or external pull script |

For total sovereignty, eventually run your own Vault or use sops + age with KMS fallback.

---

## 📦 Orchestration via GitHub Actions

GitHub Actions is perfect and free for:
- CI pipeline: build Docker images
- CD: push YAML/Helm updates to master node
- Key distribution: limited, for one-off bootstrap tasks

### 🔧 Setup Flow (Helm + K3s + Actions)
1. Bootstrap master-node manually using k3s:
   ```sh
   curl -sfL https://get.k3s.io | sh -
   ```
2. Grab the token:
   ```sh
   cat /var/lib/rancher/k3s/server/node-token
   ```
3. Join worker-node-1 and worker-node-2:
   ```sh
   curl -sfL https://get.k3s.io | K3S_URL=https://<MASTER_IP>:6443 K3S_TOKEN=<TOKEN> sh -
   ```
4. GitHub Action builds and pushes YAMLs:
   - Create kubeconfig on master
   - Copy to GitHub Secret (KUBECONFIG_BASE64)
   - GitHub Action base deploy:
     ```yaml
     steps:
       - name: Decode kubeconfig
         run: echo "${{ secrets.KUBECONFIG_BASE64 }}" | base64 --decode > kubeconfig
       - uses: azure/setup-kubectl@v3
       - run: kubectl --kubeconfig=kubeconfig apply -f ./deployments/
     ```
5. You can use a Helm chart like:
   ```sh
   helm upgrade --install api ./charts/api --kubeconfig=kubeconfig
   ```

---

## 🧠 Tooling Suggestions

| Tool         | Purpose                                              |
|--------------|------------------------------------------------------|
| k3sup        | Fast cluster bootstrap from laptop (optional)        |
| fluxcd/argo-cd| GitOps sync for passive deployment updates          |
| sops + age   | For encrypted secrets in repo                       |
| watchtower   | Auto-restart updated Docker containers (in k8s)     |
| netdata      | Full-stack observability with zero config            |
| traefik      | Ingress with Let's Encrypt, TLS, and dashboard      |

---

## 🔥 Bonus Flow: "Push-to-Infra"
1. Push to main
2. GitHub Action:
   - Builds Docker image
   - Pushes to Docker Hub or GitHub Registry
   - Applies Helm/YAML to Lightsail master node
3. Grafana + Netdata log it live

You now have a one-click deploy to a sovereign, vendor-free Farcaster backend stack.

---

## Contents
- `k8s/`: Kubernetes manifests for deployment (Ingress, Deployment, Service).

---

For more information, see the root README. 