# Procela — Evaluation build

Run the whole Procela platform on your own machine from pre-built images.
No source checkout, no build step — just Docker.

> **This is an evaluation build.** Sign-in is passwordless, a demo tenant is
> loaded for you, and outbound email is captured locally. It is not a
> production deployment.
>
> By installing or running it you agree to the Evaluation License Agreement in
> [`EULA.txt`](./EULA.txt).

---

## 1. Prerequisites

- **Docker** with Compose v2 (`docker compose version`). Docker Desktop
  (macOS / Windows) or Docker Engine (Linux). ~2 GB free disk, ~2 GB RAM.
- That's it — no Node, no database to install.

## 2. Quick start

Download this repo (**Code → Download ZIP**, or `git clone`), then from its
folder:

```bash
cp .env.example .env          # then edit JWT_SECRET (any long random string)
docker compose -f docker-compose.eval.yml up
```

First run pulls the images and loads the demo tenant (a minute or two). When it
settles, open:

- **App:** http://localhost:3000
- **Mail inbox:** http://localhost:8025 (password-reset / "Report a problem" mail lands here)

Sign in from the login screen's **Test Accounts** — e.g. *Eleanor Briggs*
(Super Admin) or *Susan Chen* (Editor) — or type any name/email.

Stop with `Ctrl-C`. To stop and wipe everything (including the database):

```bash
docker compose -f docker-compose.eval.yml down -v
```

## 3. What's running

| Service    | Purpose                                             | Host port |
|------------|-----------------------------------------------------|-----------|
| `frontend` | Web UI (nginx; reverse-proxies `/api` → backend)    | 3000      |
| `backend`  | API server (REST + AI + MCP)                        | 3001      |
| `postgres` | Database                                            | —         |
| `redis`    | Cache / sessions                                    | —         |
| `mailpit`  | Local SMTP catcher + web inbox                      | 8025      |
| `migrate`  | Applies the DB schema, then exits                   | —         |
| `eval-seed`| Loads the demo tenant on first boot, then exits     | —         |

The demo seed **skips itself if an organization already exists**, so
restarting never wipes data you added. To reload it deliberately:

```bash
docker compose -f docker-compose.eval.yml run --rm eval-seed
```

(or use **Settings → Data → Load demo data** in the app, as a Super Admin).

## 4. Common tweaks (`.env`)

- **Pin a version** — `PROCELA_TAG` defaults to `latest` (the newest evaluation
  build). Set it to a specific version like `0.1.1` for a fixed evaluation.
- **Try the AI features** — set `AI_FEATURES_ENABLED=true` and add
  `AI_PROVIDER` + the matching key (e.g. `ANTHROPIC_API_KEY`). Off by default,
  so the stack otherwise makes no external calls.
- **Pick a different demo industry** — `DEMO_INDUSTRY=healthcare` (and so on;
  see the list in `.env.example`).

## 5. Learn the product

- In the app: the **?** help icons, and the **Training Guide** — a ~90-minute
  click-by-click walkthrough, linked from in-app help.

## 6. If an image pull fails ("unauthorized")

The images are public, so a normal `docker compose up` just works. If your
network blocks anonymous GHCR pulls, log in with any GitHub account:

```bash
echo "$GH_TOKEN" | docker login ghcr.io -u <your-github-username> --password-stdin
```

---

Questions about your evaluation? Contact **legal@procela.ai**.
