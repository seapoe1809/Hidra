



# Darnahi · Project Hidra v1.0

Hidra is an option to allow health providers to set up direct risk pool to allow patients to sign up for a base plan. Paying a token amount gives you access best rate for health services.ndin
The project Hidra offers Plan 1.1 (cost of good+10% margin best rate) for Individual.

Encrypted enrollment, identity, and Bitcoin payments for hospitals and individuals.
This allows providers, pharmacies, labs to connect directly with patients. For outside US only.

A FastAPI backend + vanilla-JS frontend rebuild of the original Gradio prototype.
Individuals enroll with a hospital, get an encrypted Nostr identity (their messenger
key **is** their ID), and pay a flat monthly plan in BTC. Hospitals run a dashboard to
read their encrypted mailbox, verify membership, audit on-chain payments, and bill.

## Using the app

1. **Provider → Register Hospital.** Fill in name, address, BTC address. Leave the
   **npub** blank to auto-generate a key — **save the shown nsec**, it's your dashboard
   login token. Pick services (inpatient, clinic, hospice, home health, or type your own).
2. Copy the **member enrollment link** (also available at the bottom of the dashboard).
   It carries the hospital's npub, so members' intake DMs land in *your* mailbox.
3. **Members** open the link, enroll, and receive a private token (nsec). Their info is
   encrypted and sent to the hospital hub.
4. **Dashboard:**
   - **Messenger** — read/reply to the encrypted mailbox; intake messages are parsed
     into members automatically.
   - **ID Check** — prove membership with any **2 of** {npub, year of birth, last name,
     place of birth} via pre-stored hashes.
   - **BTC Audit** — pull on-chain transactions, link them to members, search, paginate.
   - **Bills** — add bills by npub with amount, date, and a PDF/photo; search; mark paid.

---
## Quick start

Two ways to run it — pick one.

### Option A — local virtualenv + setup script

Needs **Python 3.10+**, **git**, and **Docker** (for the database only — or point
`HIDRA_DATABASE_URL` at your own Postgres).

```bash
git clone https://github.com/seapoe1809/Hidra
cd hidra
python3 setup.py     # creates the venv, installs deps, generates secrets, starts the DB
./darnahi            # starts the app at http://localhost:8000
```

`setup.py` is idempotent — re-run it any time. `./darnahi` is all you need afterwards.

### Option B — Docker Compose (whole stack, one command)

Needs only **Docker**. Builds the app and starts PostgreSQL together:

```bash
git clone https://github.com/seapoe1809/Hidra
cd hidra
docker compose up -d --build
```

Open **http://localhost:8000** and **set up a Provider first** (step 1).

```bash
docker compose logs -f app   # follow logs
docker compose down          # stop (data persists in named volumes)
```

Set strong secrets by creating a `.env` next to `docker-compose.yml` before `up`:

```
HIDRA_PROVIDER_TOKEN=<long-random-string>
HIDRA_DB_PASSWORD=<long-random-string>
HIDRA_PORT=8000
```

## Project layout

```
hidra/
├── backend/            FastAPI app
│   ├── main.py         entry point (routers, middleware)
│   ├── config.py       env-driven settings (+ .env loader)
│   ├── db.py           PostgreSQL persistence
│   ├── routers/        API endpoints (enroll, provider, messaging, identity, audit, bills, …)
│   └── services/       nostr, identity hashing, audit, payments, contracts, parsing
├── frontend/           static SPA (no build step) — landing, provider, user, messenger, audit, bills
├── requirements.txt
├── setup.py            first-run installer (Option A)
├── darnahi             launcher (./darnahi)
├── Dockerfile          app image (Option A)
├── docker-compose.yml  app + PostgreSQL stack (Option B)
├── .env.example        documented configuration
└── ignore/             excluded legacy/scratch files (git-ignored)
```



## Troubleshooting

- **`Database did not become ready`** — check `docker logs hidra-pg`; ensure port `5433`
  is free, or set `HIDRA_DATABASE_URL` to your own Postgres and re-run `setup.py`.
- **Messenger shows "Reading relays…" for a few seconds** — it queries live Nostr relays
  on first load; that's expected.
- **Port already in use** — start on another port: `HIDRA_PORT=8001 ./darnahi`.

## NOTE
Built with AI
