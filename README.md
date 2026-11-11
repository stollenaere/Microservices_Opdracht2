# IT Support Stack (Microservices Opdracht 2)
NL + ENG version

---


## NL

Docker Compose stack voor IT-support: ticketing, Knowledge base en monitoring.

### Overzicht

| Service | Container | Image | Poort | Doel |
|---------|-----------|-------|-------|------|
| FreeScout | `ticket` | `nfrastack/freescout:2.2.24-alpine` | 80 | Ticketsysteem / helpdesk |
| MariaDB (FreeScout) | `ticket_db` | `mariadb:13.0.2` | - | Database voor FreeScout |
| BookStack | `wiki` | `solidnerd/bookstack:26.9` | 8080 | Wiki / kennisbank |
| MariaDB (BookStack) | `wiki_db` | `mariadb:13.0.2` | - | Database voor BookStack |
| Uptime Kuma | `kuma` | `louislam/uptime-kuma:2.5.9` | 8081 | Monitoring / uptime |

### Vereisten

- Docker Engine
- Docker Compose plugin (v2)
- Vrije poorten: `80`, `8080`, `8081`

### Installatie

1. Repo clonen:
   ```bash
   git clone https://github.com/<user>/<repo>.git
   cd <repo>
   ```

2. `.env` bestand aanmaken naast `compose.yaml` (zie [Environment variabelen](#environment-variabelen)).

3. Stack starten:
   ```bash
   docker compose up -d
   ```

4. Status controleren:
   ```bash
   docker compose ps
   docker compose logs -f
   ```

### Environment variabelen

Maak een `.env` bestand aan met onderstaande variabelen:

```env
# FreeScout
FREESCOUT_ADMIN_FIRSTNAME=
FREESCOUT_ADMIN_LASTNAME=
FREESCOUT_ADMIN_EMAIL=
FREESCOUT_ADMIN_PASS=
FREESCOUT_DB_PASS=

# BookStack
WIKI_DB_PASSWORD=
WIKI_APP_URL=
WIKI_APP_KEY=

# MariaDB
MARIADB_ROOT_PASSWD=
```

> **Let op:** voeg `.env` toe aan `.gitignore` zodat er geen wachtwoorden in GitHub terechtkomen.

`WIKI_APP_KEY` genereren:
```bash
docker run --rm solidnerd/bookstack:26.9 php artisan key:generate --show
```

### Toegang

| Service | URL |
|---------|-----|
| FreeScout | http://<host>:80 |
| BookStack | http://<host>:8080 |
| Uptime Kuma | http://<host>:8081 |

Standaard BookStack login bij eerste start: `admin@admin.com` / `password` (onmiddellijk wijzigen).

### Netwerk

Alle services (behalve Uptime Kuma) draaien in het `support` bridge-netwerk.

- Subnet: `172.20.0.0/16`
- IP-range: `172.20.10.0/24`
- Gateway: `172.20.10.1`
- IPv6: uitgeschakeld

### Volumes

| Volume | Gebruik |
|--------|---------|
| `ticket_data` | FreeScout modules, logs, attachments |
| `free_db_files` | FreeScout database |
| `wiki_public` | BookStack publieke uploads |
| `wiki_storage` | BookStack storage uploads |
| `book_db_files` | BookStack database |
| `kuma_data` | Uptime Kuma data |

### Beheer

```bash
# Stack stoppen
docker compose down

# Stack updaten (nieuwe images)
docker compose pull
docker compose up -d

# Logs van één service
docker compose logs -f ticket

# Backup van een database
docker exec ticket_db mariadb-dump -u root -p freescout > freescout_backup.sql
docker exec wiki_db mariadb-dump -u root -p bookstack > bookstack_backup.sql
```

> `docker compose down -v` verwijdert ook alle volumes (en dus alle data).

### Opmerkingen

- Uptime Kuma heeft toegang tot `/var/run/docker.sock` om containers te kunnen monitoren.

---


## ENG

Docker Compose stack for IT support: ticketing, knowledge base and monitoring.

### Overview

| Service | Container | Image | Port | Purpose |
|---------|-----------|-------|------|---------|
| FreeScout | `ticket` | `nfrastack/freescout:2.2.24-alpine` | 80 | Ticketing system / helpdesk |
| MariaDB (FreeScout) | `ticket_db` | `mariadb:13.0.2` | - | Database for FreeScout |
| BookStack | `wiki` | `solidnerd/bookstack:26.9` | 8080 | Wiki / knowledge base |
| MariaDB (BookStack) | `wiki_db` | `mariadb:13.0.2` | - | Database for BookStack |
| Uptime Kuma | `kuma` | `louislam/uptime-kuma:2.5.9` | 8081 | Monitoring / uptime |

### Requirements

- Docker Engine
- Docker Compose plugin (v2)
- Free ports: `80`, `8080`, `8081`

### Installation

1. Clone the repo:
   ```bash
   git clone https://github.com/<user>/<repo>.git
   cd <repo>
   ```

2. Create a `.env` file next to `compose.yaml` (see [Environment variables](#environment-variables)).

3. Start the stack:
   ```bash
   docker compose up -d
   ```

4. Check the status:
   ```bash
   docker compose ps
   docker compose logs -f
   ```

### Environment variables

Create a `.env` file with the following variables:

```env
# FreeScout
FREESCOUT_ADMIN_FIRSTNAME=
FREESCOUT_ADMIN_LASTNAME=
FREESCOUT_ADMIN_EMAIL=
FREESCOUT_ADMIN_PASS=
FREESCOUT_DB_PASS=

# BookStack
WIKI_DB_PASSWORD=
WIKI_APP_URL=
WIKI_APP_KEY=

# MariaDB
MARIADB_ROOT_PASSWD=
```

> **Note:** add `.env` to `.gitignore` so no passwords end up on GitHub.

Generate `WIKI_APP_KEY`:
```bash
docker run --rm solidnerd/bookstack:26.9 php artisan key:generate --show
```

### Access

| Service | URL |
|---------|-----|
| FreeScout | http://<host>:80 |
| BookStack | http://<host>:8080 |
| Uptime Kuma | http://<host>:8081 |

Default BookStack login on first start: `admin@admin.com` / `password` (change immediately).

### Network

All services (except Uptime Kuma) run in the `support` bridge network.

- Subnet: `172.20.0.0/16`
- IP range: `172.20.10.0/24`
- Gateway: `172.20.10.1`
- IPv6: disabled

### Volumes

| Volume | Purpose |
|--------|---------|
| `ticket_data` | FreeScout modules, logs, attachments |
| `free_db_files` | FreeScout database |
| `wiki_public` | BookStack public uploads |
| `wiki_storage` | BookStack storage uploads |
| `book_db_files` | BookStack database |
| `kuma_data` | Uptime Kuma data |

### Management

```bash
# Stop the stack
docker compose down

# Update the stack (new images)
docker compose pull
docker compose up -d

# Logs of a single service
docker compose logs -f ticket

# Database backup
docker exec ticket_db mariadb-dump -u root -p freescout > freescout_backup.sql
docker exec wiki_db mariadb-dump -u root -p bookstack > bookstack_backup.sql
```

> `docker compose down -v` also removes all volumes (and therefore all data).

### Notes

- Uptime Kuma has access to `/var/run/docker.sock` so it can monitor containers.
