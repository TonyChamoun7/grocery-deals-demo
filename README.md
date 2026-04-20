# Ontario Grocery Deals

![Next.js](https://img.shields.io/badge/Frontend-Next.js-black)
![TypeScript](https://img.shields.io/badge/Language-TypeScript-blue)
![FastAPI](https://img.shields.io/badge/Backend-FastAPI-green)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-blue)
![Neon](https://img.shields.io/badge/Database-Neon-00E599)
![Render](https://img.shields.io/badge/Deployment-Render-purple)
![SQLAlchemy](https://img.shields.io/badge/ORM-SQLAlchemy-red)
![Flipp](https://img.shields.io/badge/Data-Flipp%20Flyers-orange)

A production-style grocery flyer comparison app built with **Next.js, TypeScript, Tailwind CSS, FastAPI, SQLAlchemy, Neon PostgreSQL, and Render**.

Ontario Grocery Deals helps shoppers compare weekly flyer prices with historical context, so sale stickers are easier to judge. The app combines **live flyer ingestion, product matching, historical price tracking, deal signals, region-based browsing, admin review workflows, and cloud deployment** into one full-stack product.

---

## Live Demo

- **Frontend:** https://grocery-deals-web.onrender.com
- **Backend API:** https://grocery-deals-api-4ib5.onrender.com
- **API Health Check:** https://grocery-deals-api-4ib5.onrender.com/health

The backend root URL returning `404` is expected because the API exposes named routes such as `/health`, `/products`, and `/stores` rather than a root landing page.

---

## Overview

Most grocery flyer tools show current deals, but they do not explain whether a sale is actually good compared with recent flyer history. This project was built to make grocery prices easier to evaluate across Ontario regions by combining current flyer data with prior weekly lows and median historical prices.

A major goal was to build something closer to a real deployed product than a static portfolio demo, including:

- live Flipp flyer sync
- canonical product catalog and alias matching
- historical price snapshots
- region-based product browsing
- current offer comparison
- admin import and review workflow
- cloud-hosted PostgreSQL persistence
- production deployment across Render and Neon
- practical debugging of real deployment, CORS, database, and long-running sync issues

---

## Core Features

### Product Browsing

The main shopping experience lets users browse current grocery deals by region, aisle, and product category.

- browse products across Ontario regions
- filter by category and subcategory
- sort by best deal
- search by product name
- view current best price
- see store and region for the best current offer
- open product detail pages for deeper price context

### Deal Signals

Each product is evaluated against historical flyer context rather than shown as a deal by default.

- current low price
- median prior weekly low
- previous low before the current week
- percentage above or below usual price
- deal labels such as:
  - new low
  - below usual sale
  - typical sale
  - not a strong deal
  - need more history

This helps distinguish real savings from ordinary weekly flyer noise.

### Product Detail Pages

Each product has a detail view focused on explaining why the current price is or is not interesting.

- current lowest offer
- current store and region
- current offers list
- tracked comparable price history
- prior weekly low comparison
- median historical comparison
- raw flyer item descriptions to clarify what each offer actually refers to

The detail pages are designed to reduce ambiguity around similar grocery items, such as different cuts of meat, seafood formats, package sizes, and produce variations.

### Current Offers

The current offers section compares live flyer rows for the selected product.

- store name
- region
- flyer item title
- current price
- week start and end dates
- deal rating badge

This is useful for quickly checking whether one store is meaningfully better than another for the same product.

### Tracked Comparable Prices

The history section shows previous comparable flyer prices for the product.

- prior tracked prices
- store and region
- flyer item description
- tracked price
- historical context used for the deal signal

Descriptions are included so users can understand what the historical price referred to, instead of seeing an unexplained number.

### Price Check

The app includes a price-check workflow for comparing an entered price against flyer and Statistics Canada context.

- choose a product
- enter a price
- optionally select a region
- compare against flyer history
- compare against StatCan benchmark data where available
- receive a plain-language verdict

### Admin Import Workflow

The admin area supports both automatic and manual flyer maintenance.

- one-click Flipp sync by configured region
- sync all configured regions
- view recent flyer weeks
- inspect pending flyer items
- map flyer rows to canonical products
- import structured flyer items manually
- review region coverage health
- identify repeated unmapped flyer titles

The admin tools are intentionally included because flyer data is messy, and a real product needs a cleanup workflow instead of pretending all scraped data is perfect.

### Automatic Flipp Sync

The app can sync current flyer data from Flipp for configured Ontario regions.

The sync pipeline:

1. Loads the current flyer index for a configured postal code
2. Filters to supported grocery merchants
3. Skips expired or unavailable flyers
4. Fetches flyer item rows
5. Imports flyer weeks and flyer items
6. Matches rows to canonical products when possible
7. Creates current offers and historical price snapshots
8. Leaves uncertain items in the review queue

The production sync was optimized for Render free-tier constraints by bounding the number of flyers and rows imported per run.

### Region Coverage Health

The admin dashboard includes a coverage summary to show where the data needs attention.

- store depth by region
- live product count
- pending item count
- latest tracked week
- top repeated unmapped titles

This makes it easier to decide what cleanup work matters instead of reviewing thousands of raw flyer rows manually.

---

## Tech Stack

### Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS
- Shared TypeScript contracts

### Backend

- FastAPI
- Python
- SQLAlchemy
- Pydantic
- Uvicorn

### Database

- Neon PostgreSQL
- SQL migrations
- Seed data for local development and catalog expansion

### Deployment

- Render web service for the Next.js frontend
- Render web service for the FastAPI backend
- Neon hosted PostgreSQL database

### Data Sources

- Flipp flyer data
- Statistics Canada benchmark data
- Manually curated product aliases and catalog seeds

---

## Screenshots

### Overview

![Overview](overview.png)

### Browse Products

![Browse Products](screenshots/browse-products.png)

### Product Detail

![Product Detail](screenshots/product-detail.png)

### Current Offers and Price History

![Current Offers and Price History](screenshots/current-offers-history.png)

### Price Check

![Price Check](screenshots/price-check.png)

### Admin Import

![Admin Import](screenshots/admin-import.png)

### Region Coverage

![Region Coverage](screenshots/region-coverage.png)

### Pending Review

![Pending Review](screenshots/pending-review.png)

---

## Architecture

The application uses a separated frontend/backend architecture with a shared type package and PostgreSQL persistence.

### High-Level Design

- **Next.js frontend** handles routing, product browsing, admin UI, price check flows, and server-side proxy routes
- **FastAPI backend** exposes REST endpoints for stores, products, offers, history, admin imports, and Flipp sync
- **Neon PostgreSQL** stores products, aliases, stores, flyer weeks, flyer items, offers, and price history snapshots
- **Render** hosts both the frontend and backend services
- **Shared TypeScript package** keeps frontend API contracts consistent

### Data Flow

1. The admin triggers a Flipp sync for a region
2. The frontend calls a same-origin Next.js proxy route
3. The proxy sends an authenticated request to the FastAPI backend
4. The backend fetches supported flyer data from Flipp
5. Flyer rows are imported into PostgreSQL
6. Matching logic maps known rows to canonical products
7. Offers and historical price snapshots are created
8. The frontend displays current best prices and deal signals

### Main Backend Data Models

- categories
- products
- product aliases
- stores
- flyer weeks
- flyer items
- offers
- price history snapshots

---

## Engineering Highlights

This project involved more than page-level UI. A large part of the work was stabilizing a real full-stack deployment with live data ingestion and imperfect third-party flyer data.

Some of the issues solved during development included:

- PostgreSQL serial sequence repair after manual imports
- product matching bugs that caused incorrect prices, such as bad bacon and seafood matches
- missing product detail routes after catalog/filter mismatches
- historical median and prior-low calculations
- raw flyer descriptions in comparable price history to reduce ambiguity
- Render deployment setup without using paid Blueprint databases
- Neon PostgreSQL configuration and schema initialization
- SQLAlchemy driver compatibility for production PostgreSQL URLs
- CORS and admin session handling across deployed frontend/backend services
- Next.js production redirect issues caused by localhost URL construction
- long-running Flipp sync timeouts on Render free tier
- flaky Flipp flyer endpoints returning `404` for listed flyers
- production sync optimization by bounding supported merchants, active flyers, and imported row counts

The final sync flow is intentionally pragmatic: it prioritizes reliable weekly flyer comparison over trying to crawl every possible flyer row from Flipp.

---

## Product Decisions

### Historical Context Over Sale Labels

The app does not assume a flyer price is good just because it appears in a flyer. Deal strength is based on prior observed weekly lows and median historical pricing.

### Canonical Products and Aliases

Flyer titles are messy and inconsistent, so the app uses canonical products with aliases instead of relying only on raw text search. Uncertain rows are left for review instead of being mapped aggressively.

### Region-Based Store Tracking

For production reliability, automatic Flipp sync tracks stores at a merchant-region level, such as `Metro Ottawa`, rather than depending on slower exact store-location endpoints for every flyer.

### Admin Review Instead of Perfect Automation

The admin workflow is part of the product because real grocery flyer data requires human correction. The app surfaces repeated unmapped titles so cleanup can be targeted.

### Render-Free-Tier Friendly Sync

The sync pipeline is bounded so it can run reliably on a free deployment. This makes the deployed demo usable without requiring paid infrastructure.

---

## Local Development

### Prerequisites

- Node.js 24+
- Corepack / pnpm
- Python 3.13+
- Docker Desktop

### Start PostgreSQL

```powershell
docker compose up -d db
```

### Install Frontend Dependencies

```powershell
corepack enable
corepack pnpm install --dir apps/web
```

### Install Backend Dependencies

```powershell
py -m pip install -e "apps/api[dev]"
```

### Apply Schema and Seed Data

```powershell
Get-Content infra/db/migrations/0001_initial_schema.sql | docker exec -i grocery-deals-db psql -U postgres -d grocery_deals
Get-Content infra/db/seeds/0001_sample_data.sql | docker exec -i grocery-deals-db psql -U postgres -d grocery_deals
```

### Run Backend

```powershell
py -m uvicorn app.main:app --reload --app-dir apps/api
```

### Run Frontend

```powershell
corepack pnpm --dir apps/web dev
```

Default local URLs:

- Web: http://localhost:3000
- API: http://localhost:8000
- API docs: http://localhost:8000/docs
- PostgreSQL: localhost:5434

---

## Environment Variables

### Web

```text
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
API_BASE_URL=http://localhost:8000
NEXT_PUBLIC_SITE_URL=http://localhost:3000
ADMIN_PASSWORD=local-admin-password
ADMIN_SESSION_SECRET=local-admin-session-secret
```

### API

```text
API_DATABASE_URL=postgresql+psycopg://postgres:postgres@localhost:5432/grocery_deals
API_CORS_ORIGINS=http://localhost:3000
ADMIN_PASSWORD=local-admin-password
ADMIN_SESSION_SECRET=local-admin-session-secret
FLIPP_API_SERVER=https://dam.flippenterprise.net/api/flipp
FLIPP_LOCALE=en
FLIPP_TIMEOUT_SECONDS=20
FLIPP_VERIFY_SSL=true
FLIPP_REGION_POSTAL_CODES=ottawa:K1A0A1,toronto:M4B1B3,peel:L5B1M1,york:L4J1V8,oakville:L6J1H6
```

---

## Useful Commands

```powershell
make dev-db
make run-api
make run-web
make test-api
corepack pnpm --dir apps/web build
corepack pnpm --dir apps/web exec tsc --noEmit
py -m pytest apps/api/tests
```

---

## API Surface

### Public

- `GET /health`
- `GET /stores`
- `GET /regions`
- `GET /catalog/filters`
- `GET /products`
- `GET /products/{product_id}`
- `GET /products/{product_id}/current-offers`
- `GET /products/{product_id}/price-history`
- `POST /price-check`

### Admin

- `GET /admin/flyer-weeks`
- `GET /admin/flyer-weeks/{flyer_week_id}/items`
- `POST /admin/flyer-weeks`
- `POST /admin/flyer-items/import`
- `POST /admin/flyer-items/{id}/map-product`
- `GET /admin/products/options`
- `GET /admin/coverage/regions`
- `GET /admin/sync/flipp/targets`
- `POST /admin/sync/flipp`

---

## Monorepo Layout

```text
grocery-deals/
  apps/
    api/                 FastAPI backend
    web/                 Next.js frontend
  docs/
    architecture/        deployment and architecture notes
    product/             product planning notes
  infra/
    db/                  migrations and seed data
  packages/
    shared-types/        shared TypeScript contracts
```

---

## Future Improvements

Planned improvements that would further strengthen the product include:

- background job queue for full multi-region syncs
- scheduled weekly sync automation
- richer admin tools for bulk alias creation
- automatic junk-row filtering for low-value flyer items
- more product-specific matching rules
- charts for price history trends
- richer mobile UI polish
- CI/CD checks for frontend and backend
- monitoring and sync run logs
- user-facing explanation tooltips for deal signals
