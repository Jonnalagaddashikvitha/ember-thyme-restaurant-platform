
# Ember & Thyme – Restaurant Management & Billing Platform

Full Stack Web Development (21CSE354T) – Task 4
Full-stack restaurant platform for **Ember & Thyme / L'Aura Cucina & Grill**: table-side ordering, a Kitchen Display System (KDS), and smart billing with promo codes, GST, tips and split payments.

| | |
|---|---|
| Student | J. Shikvitha |
| Register number | RA2411003050365 |
| Program | B.Tech CSE – F, III Year / V Semester |
| Institution | SRM Institute of Science and Technology, Tiruchirappalli |
| Academic year | 2026 – 2027 |

## Features

- **Order placement:** pick a table, add dishes, send to the kitchen. The table becomes `OCCUPIED`.
- **Kitchen Display System:** tickets move `RECEIVED → PREPARING → READY → SERVED`. Skipped or backward steps are rejected.
- **Smart billing:** promo codes, 5% GST, custom tip and an N-way split in one transaction.
- **Invoices:** unique numbers in the form `INV-2026-XXXX`, starting at `INV-2026-1001`.
- **Table turnover:** settling a bill marks the order `PAID` and resets the table to `AVAILABLE`.
- **E2E verification suite:** an 11-step check of the whole lifecycle on Table 04.

## Billing rules

```
Discounted subtotal = Subtotal − PromoDiscount
GST (5%)            = Discounted subtotal × 0.05
Final payable       = Discounted subtotal + GST + Tip
Per-person split    = Final payable ÷ SplitCount
```

| Promo code | Discount |
|---|---|
| `WELCOME10` | 10% |
| `AURA20` | 20% |

Money uses `BigDecimal` with `RoundingMode.HALF_UP` in the backend (whole paise in the browser demo), so there is no floating-point drift.

## REST API (`/api/v1`)

| Method and endpoint | Purpose |
|---|---|
| `GET /menu` | Active menu grouped by course, with dietary info |
| `POST /orders` | Create an order, lock the table as `OCCUPIED`, save item snapshots (`@Transactional`) |
| `GET /kds/orders` | Active kitchen queue |
| `PATCH /kds/orders/{id}/status?nextStatus=` | Advance a ticket one stage (`@Transactional`) |
| `POST /billing/settle` | Apply promo, GST and tip, split, create invoice, mark paid, reset table (`@Transactional`) |

CORS allows the frontend origin `http://localhost:5173`.

## Tech stack

- **Frontend:** React 19, TypeScript, Vite (port 5173), typed client in `src/services/api.ts`
- **Backend:** Spring Boot REST API (port 8080)
- **Database:** PostgreSQL 18 (port 5432)

## Run the browser demo

`demo/index.html` is a self-contained version of the app. A small in-page `api` object stands in for the Spring Boot endpoints, so it needs no server or database. Open the file in any modern browser. Data resets on reload.

Tabs: **Floor & order**, **Kitchen display**, **Billing**, **E2E test suite**.

## Run the full stack

1. Start PostgreSQL and create a database for the app.
2. Start the backend from `backend/` on port 8080 (for example `./mvnw spring-boot:run`).
3. Start the frontend from `frontend/`:
   ```bash
   npm install
   npm run dev
   ```
4. Open `http://localhost:5173`. To point at a different backend, set `VITE_API_URL` (default `http://localhost:8080/api/v1`).

## 11-step E2E flow

1. Health and CORS preflight
2. Table 04 starts `AVAILABLE`
3. Load menu catalog
4. Place a multi-item order
5. Table 04 becomes `OCCUPIED`
6. KDS advances to `PREPARING`
7. KDS advances to `READY`
8. KDS advances to `SERVED`
9. Settle with `AURA20`, 10% tip, 2-way split
10. Invoice `INV-2026-XXXX` is persisted
11. Table 04 resets to `AVAILABLE`

## Repository layout

```
├── backend/     Spring Boot service (controller, service, model, repository, dto)
├── frontend/    React 19 + TypeScript client
├── demo/        index.html – single-file browser demo
├── docs/        Task 4 report (task_4_shik.docx)
└── README.md
```

## Notes

Submitted for academic coursework, SRM IST Trichy Campus, Department of Computer Science and Engineering.

