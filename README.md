# TwentyCRM — Best Practice Deployment

A small, opinionated deployment setup for [TwentyCRM](https://twenty.com) using Docker Compose.

## Quick start

```bash
cp .env_example .env
# edit .env and set your secrets / domain
docker compose up -d
```

Then open the app in your browser.

## Creating your workspace

> ⚠️ **Important:** Your S3 bucket (integrated RustFS or external) must be **ready and reachable** *before* creating the admin user and workspace. Twenty writes initial assets to storage during bootstrap — if the bucket isn't available, workspace creation will fail or leave you in a broken state.

1. Open the web app and use the **console** to generate your CRM workspace (admin user + initial workspace).
2. Once the workspace is created and you've signed in, **disable the console** in your `.env` (set the relevant flag to `false`) and restart:

   ```bash
   docker compose up -d
   ```

This avoids leaving the bootstrap console exposed in production.

## Storage options

### Option A — Integrated RustFS (default, simplest)

The bundled `rustfs` service in `docker-compose.yml` is scoped to this Twenty instance only. Nothing to configure — it just works for a single-tenant deployment.

### Option B — Shared external RustFS

If you run a separate RustFS instance (or cluster) shared across multiple apps, point Twenty at it by generating a **dedicated** set of credentials per Twenty instance:

- `STORAGE_S3_ACCESS_KEY_ID` — unique per instance
- `STORAGE_S3_SECRET_ACCESS_KEY` — unique per instance
- `STORAGE_S3_BUCKET` — unique bucket per instance
- `STORAGE_S3_ENDPOINT` — your RustFS endpoint

Each Twenty instance should have its **own access key, secret, and bucket** so they're isolated from each other.

## Files

- `docker-compose.yml` — services (Twenty server, worker, db, rustfs)
- `.env_example` — copy to `.env` and fill in
- `.env` — your local secrets (gitignored)
