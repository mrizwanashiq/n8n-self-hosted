# Integration Guide

How to trigger n8n workflows and interact with its API from any application.

## Webhook Triggers (Recommended)

The simplest way to run a workflow from external code is an HTTP webhook.

1. In n8n, create a workflow with a **Webhook** trigger node
2. Set the method (GET or POST) and copy the webhook URL
3. **Activate** the workflow (the toggle at the top right)
4. Call it from your app:

```bash
curl -X POST https://your-n8n-domain.com/webhook/your-path \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
```

```js
await fetch("https://your-n8n-domain.com/webhook/your-path", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ key: "value" }),
});
```

---

## REST API

n8n exposes a REST API to manage workflows and executions programmatically.

### Enable API Access

In n8n: **Settings → API → Create API key**

### Common Endpoints

```bash
# List all workflows
curl https://your-n8n-domain.com/api/v1/workflows \
  -H "X-N8N-API-KEY: your-api-key"

# Trigger a workflow by ID
curl -X POST https://your-n8n-domain.com/api/v1/workflows/123/run \
  -H "X-N8N-API-KEY: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}'

# Get executions
curl https://your-n8n-domain.com/api/v1/executions \
  -H "X-N8N-API-KEY: your-api-key"
```

Full API reference: https://docs.n8n.io/api/

---

## Data Flow

```
Your Application
    │
    ▼  POST /webhook/path  (or REST API call)
n8n receives trigger
    │
    ▼
Workflow executes nodes
    │
    ▼
Returns JSON response
    │
    ▼
Your Application handles result
```

---

## Security

- Set `N8N_ENCRYPTION_KEY` to encrypt stored credentials
- Enable basic auth (`N8N_BASIC_AUTH_ACTIVE=true`) or configure a reverse proxy with HTTPS when exposing n8n publicly
- Use webhook paths that are not guessable, or add `Header Auth` to your webhook node

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| Webhook returns 404 | Workflow is not activated — toggle it on |
| Webhook returns 404 after activation | `WEBHOOK_URL` env var doesn't match your public domain |
| API returns 401 | API key missing or wrong header name (`X-N8N-API-KEY`) |
| Workflow runs but returns wrong data | Check the **Execution log** in the n8n UI for node output |
