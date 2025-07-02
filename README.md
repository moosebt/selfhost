# authentik-selfhost

Docker Compose config for running Authentik in a homelab.

---

## Prerequisites

- Docker and Docker Compose v2
- External Docker networks: `frontend`, `backend`
- Secrets placed in `./secrets` directory

---

## Setup

Clone the repository:

```bash
git clone https://github.com/<your-username>/authentik-selfhost.git
cd authentik-selfhost
```

Create required secrets:

```bash
mkdir -p secrets

openssl rand -base64 32 > secrets/authentik_secret_key.txt
openssl rand -base64 24 > secrets/postgres_password.txt
openssl rand -base64 24 > secrets/authentik_postgressql__password.txt
```

Create Docker networks if needed:

```bash
docker network create frontend
docker network create backend
```

Start services:

```bash
docker compose up -d
```

---

## Directory Structure

```
.
├── docker-compose.yaml
├── renovate.json
├── secrets/
├── certs/
├── media/
├── custom-templates/
└── .gitignore
```

---

## Services

- `postgresql`: PostgreSQL 16-alpine
- `redis`: Redis Alpine
- `server`: Authentik application
- `worker`: Authentik worker process

---

## Secrets

All secrets are mounted using Docker secrets:

- `secrets/authentik_secret_key.txt`
- `secrets/postgres_password.txt`
- `secrets/authentik_postgressql__password.txt`

