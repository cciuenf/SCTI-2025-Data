# SCTI Data Database

This repository contains a **PostgreSQL database** with depersonalized SCTI data.
It is bundled with a `docker-compose.yml` so you can quickly spin up a local instance for development, testing, or analytics.

---

## 🚀 Getting Started

### Requirements

* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)

### Start the Database

```bash
docker-compose up -d
```

This will start PostgreSQL on:

* **Host:** `localhost`
* **Port:** `5432`
* **User:** `postgres`
* **Password:** `1234`
* **Database:** `scti-db`

### Connect to the Database

Using `psql`:

```bash
psql -h localhost -p 5432 -U postgres -d scti-db
```

Using a GUI client (e.g. TablePlus, DBeaver, Beekeeper):

* Host: `localhost`
* Port: `5432`
* Username: `postgres`
* Password: `1234`
* Database: `scti-db`

---

## 📑 Schema Overview

The schema models the **SCTI-2025** event with the following tables: events, activities, products (tickets, items), users, and purchases.

### Users & Access

* **`users`** – Registered participants, with anonymized names/emails.
* **`user_products`** – Items or tickets owned by a user (often linked to purchases).
* **`user_tokens`** – Tokens given to users for event access or activities.
* **`user_verifications`** – Temporary codes for account verification.
* **`refresh_tokens`** – Session tokens for authentication.

### Events & Activities

* **`events`** – Main events (conferences, fairs).
* **`activities`** – Talks, workshops, or sub-events within an event.
* **`activity_registrations`** – Links users to activities they registered for.
* **`event_registrations`** – Links users to entire events.
* **`event_users`** – Basic mapping of users attending an event.
* **`admin_statuses`** – Defines which users are event admins and their roles.

### Products & Purchases

* **`products`** – Tickets, items, or bundles that can be bought.
* **`product_bundles`** – Defines parent/child product relationships.
* **`event_products`** – Associates products with events.
* **`purchases`** – Records user purchases of products (with gift support).
* **`pix_purchases`** – PIX-specific purchases (Brazilian instant payment).

### Coffee Breaks

* **`coffee_breaks`** – Scheduled coffee breaks in events.
* **`coffee_registration`** / **`coffee_registrations`** – Users registered or attended specific breaks.

### Access Control

* **`access_targets`** – Defines which product grants access to which event or activity.

### Other Tables

* You can explore other tables or the schema by running `\d`

---

## 🛠 Example Queries

### List all events

```sql
SELECT id, name, start_date, end_date
FROM events;
```

### Find all users registered for a specific activity

```sql
SELECT u.id, u.name, u.last_name, u.email
FROM activity_registrations ar
JOIN users u ON ar.user_id = u.id
WHERE ar.activity_id = '<activity_id>';
```

### Get purchases with products and user info

```sql
SELECT p.id AS purchase_id, u.email, pr.name AS product_name, p.quantity
FROM purchases p
JOIN users u ON p.user_id = u.id
JOIN products pr ON p.product_id = pr.id;
```

---

## 📦 Stopping & Cleaning Up

Stop the containers:

```bash
docker-compose down
```

Stop and remove volumes (wipes all data):

```bash
docker-compose down -v
```

---

## ⚠️ Notes

* All user data has been **depersonalized** (`name = "SCTI"`, `last_name = "User N"`, `email = "scti-N@mail.com"`).
* This database is for **testing, analytics, and development** only.
* If you need a fresh import, re-run `docker-compose down -v && docker-compose up -d`.

---

✨ You now have a working local copy of the SCTI event database!

---
