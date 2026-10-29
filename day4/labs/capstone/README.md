# Capstone: QuickQuote (take-home)

**Due:** Wed 4 Nov 2026 EOD IST.
**Submit:** zip your `capstone/` folder and email it to the trainer along with the README below and two screenshots. Trainer reviews and signs off your certificate. Your lab VM, namespace and resource group stay available until then.

## The ask

Build and deploy **QuickQuote**, a tiny service:

| Endpoint | Behaviour |
|---|---|
| `GET /quote?symbol=XYZ` | Returns `{"symbol":"XYZ","price": <float>,"ts": "<iso8601>"}`. Price can be fake or random. |
| `GET /health` | Returns `200` with body `ok`. |

- Any language. Keep it tiny.
- Container: non-root user, pinned base image (no `:latest`), a healthcheck.
- Deployed to your AKS namespace with a **Helm chart** (probes and resource limits set).
- Image name starts with your namespace: `<ACR_LOGIN_SERVER>/<NAMESPACE>/quickquote:v1`.

## Before you start — re-read your lab values

When you resume on the VM, open `C:\EYLAB\azure-card.json` and note:

- `ACR_LOGIN_SERVER`, `ACR_USERNAME`, `ACR_PASSWORD` (push creds — no `az` CLI on these VMs, use `docker login` directly)
- `NAMESPACE` (your AKS namespace)
- `RESOURCE_GROUP` (your personal RG)

Push flow:

```powershell
docker login $env:ACR_LOGIN_SERVER -u $env:ACR_USERNAME -p $env:ACR_PASSWORD
docker build -t "$env:ACR_LOGIN_SERVER/$env:NAMESPACE/quickquote:v1" .
docker push     "$env:ACR_LOGIN_SERVER/$env:NAMESPACE/quickquote:v1"
```

## Put it in your fork under `capstone/`

```
capstone/
  app/
  Dockerfile
  helm/
  README.md
```

## README.md must include

1. The spec you gave Copilot (prompt and any spec file).
2. One thing Copilot got wrong, and how you caught it.
3. Screenshot: `kubectl get pods -n $env:NAMESPACE` and `helm list -n $env:NAMESPACE`.
4. Screenshot: `curl` to `/quote?symbol=MSFT` returning JSON (through `kubectl port-forward`).

## Scoring (100)

| Item | Points |
|---|---|
| `/quote` and `/health` respond through port-forward | 30 |
| Dockerfile: non-root, pinned base, healthcheck | 20 |
| Helm chart installs cleanly, probes and resources set | 25 |
| README items 1 to 4 | 25 |

## Optional stretch (+10, not required for certificate)

Add a `terraform/` folder with a `terraform plan` for a Key Vault in your own resource group (Block 1 spec style). **Plan only — do not apply.** Your SP may not have Key Vault write permissions; the plan output is what we grade. Paste the plan summary line into your README.

## Hints

- Reuse your Day 1 prompts for the Dockerfile and Helm chart.
- If you get stuck, ask Copilot with the exact error and the file attached, and read the fix before accepting it.
- Verify locally first (`docker run -p 8080:8080 ...`) before pushing to ACR.
