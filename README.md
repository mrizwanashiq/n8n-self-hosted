# n8n — Self-Hosted Workflow Automation

> Maintained by [@mrizwanashiq](https://github.com/mrizwanashiq)

Deploy [n8n](https://n8n.io) anywhere for free. This repo is a minimal, ready-to-run Docker setup that works on any platform that can run containers.

## Quick Start (Local)

```bash
cp .env.example .env
docker compose up -d
```

n8n will be available at **http://localhost:5678**

---

## Deployment Options

### Docker Compose (Recommended for self-hosted VPS)

```bash
git clone https://github.com/mrizwanashiq/n8n-self-hosted.git
cd n8n-self-hosted
cp .env.example .env
# Edit .env — at minimum set WEBHOOK_URL to your public domain
docker compose up -d
```

Data is persisted in a named Docker volume (`n8n_data`).

---

### Railway

1. Fork [mrizwanashiq/n8n-self-hosted](https://github.com/mrizwanashiq/n8n-self-hosted) on GitHub
2. Go to [railway.app](https://railway.app) → New Project → Deploy from GitHub
3. Select your fork — Railway auto-detects the Dockerfile
4. Set environment variables in the Railway dashboard:
   ```
   WEBHOOK_URL=https://<your-app>.railway.app
   N8N_ENCRYPTION_KEY=<random-32-char-string>
   ```
5. Add a volume mount at `/home/node/.n8n` to persist data

---

### Render

1. Fork [mrizwanashiq/n8n-self-hosted](https://github.com/mrizwanashiq/n8n-self-hosted), then go to [render.com](https://render.com) → New → Web Service
2. Connect your repo, set **Environment** to `Docker`
3. Add a **Disk** at mount path `/home/node/.n8n` (at least 1 GB)
4. Set environment variables:
   ```
   WEBHOOK_URL=https://<your-app>.onrender.com
   N8N_ENCRYPTION_KEY=<random-32-char-string>
   ```

---

### Fly.io

```bash
fly launch --no-deploy
fly volumes create n8n_data --size 1
```

Add to `fly.toml`:
```toml
[mounts]
  source = "n8n_data"
  destination = "/home/node/.n8n"

[[services]]
  internal_port = 5678
```

```bash
fly secrets set N8N_ENCRYPTION_KEY=<random-32-char-string>
fly secrets set WEBHOOK_URL=https://<your-app>.fly.dev
fly deploy
```

---

### DigitalOcean App Platform

1. Fork [mrizwanashiq/n8n-self-hosted](https://github.com/mrizwanashiq/n8n-self-hosted) and connect it in the DigitalOcean App Platform
2. Choose **Dockerfile** as the build method
3. Set environment variables and add a storage volume at `/home/node/.n8n`

---

### Coolify / Portainer / any self-hosted PaaS

Point it at this repo (or use the `docker-compose.yml` directly). Mount a persistent volume at `/home/node/.n8n`.

---

## Environment Variables

Copy `.env.example` to `.env` and edit as needed.

| Variable | Default | Description |
|---|---|---|
| `N8N_HOST` | `0.0.0.0` | Listen address |
| `N8N_PORT` | `5678` | Listen port |
| `WEBHOOK_URL` | `http://localhost:5678` | Public base URL (must match your domain) |
| `N8N_ENCRYPTION_KEY` | — | **Required in production** — encrypts stored credentials |
| `DB_TYPE` | `sqlite` | Set to `postgresdb` for PostgreSQL |

See `.env.example` for the full list including auth, SMTP, and PostgreSQL settings.

## Data Persistence

n8n stores all workflows, credentials, and executions in `/home/node/.n8n` inside the container. Always mount a persistent volume at that path — losing it means losing all your workflows.

## Upgrading

```bash
docker compose pull
docker compose up -d
```

## Documentation

- [n8n Docs](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [Environment Variables Reference](https://docs.n8n.io/hosting/environment-variables/)

---

Made by [@mrizwanashiq](https://github.com/mrizwanashiq)
