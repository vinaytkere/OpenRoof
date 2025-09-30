# 🏡 OpenRoof — Scope, Roadmap & Solution Layout

> A transparent, low‑cost, open real‑estate platform for **Listings • Buy • Sell • Rent • Governance** with optional **IoT** and **Legal Proof PDFs**.

---

## 1) Problem & Vision (1‑minute brief)

* **Problem:** High brokerage, opaque processes, weak evidence in tenant–owner disputes.
* **Vision:** Open, verifiable workflows for listings, buy/sell/rent, payments, notices, and dispute documentation.
* **Principles:** Transparency • Reusability • Auditability • Role‑based access • Cloud‑ready.

---

## 2) Feature Overview (by Role)

**Tenant**

* Browse/search listings (map + list), apply, schedule visits, chat (masked contact).
* Rent payments & receipts, start **vacating notice**, raise **complaints** with evidence, download **PDF proof**.

**Owner**

* Create/manage listings, verify applicants, negotiation workspace (offers/counter‑offers).
* Track rent, start/track notices, raise complaints, generate legal proof PDFs.

**Broker (optional)**

* Post on behalf of owners (with consent), manage leads, track commissions; no finance access unless delegated.

**Admin**

* KYC moderation, fraud/anomaly flags, content moderation, exportable audit trails and analytics.

---

## 3) Major Modules

* **Listings** (create/edit, media, amenities, availability, moderation)
* **Search** (filters, saved searches, alerts)
* **Buy/Sell** (inquiries → offers → negotiation notes → agreement export)
* **Rent/Lease** (applications, screening checklist, lease terms, deposits)
* **Payments** (rent logs, receipts, reminders; UPI/QR/NFC capture via app/IoT)
* **Notices** (initiate/track vacate; auto reminders)
* **Complaints & Legal PDFs** (evidence upload → signed PDF with SHA‑256 + ref ID)
* **IoT Gateway (optional, non‑enforcement)** (door‑side device shows due days, scans QR, logs events; **no auto‑lock**)
* **KYC & Verification** (doc capture; configurable by market)
* **Chat/Leads/Scheduler** (masked contact; calendar slots)
* **Admin & Moderation** (RWA/legal share options, takedowns, dashboards)

---

## 4) Architecture (Clean + Microservices)

* **API Gateway** → routes external traffic; auth, rate limits.
* **Services** (per bounded context): Listings, Search, Payments, Notices, Complaints, PdfGen, Identity, IoTGateway.
* **Async Messaging** (RabbitMQ/Cloud Queue): `PaymentRecorded`, `NoticeStarted`, `ComplaintFiled`, `PdfGenerated`.
* **Storage**: PostgreSQL (OLTP), Object Storage (evidence/PDFs), Redis (caching), Elasticsearch/Meilisearch (optional).
* **Observability**: Serilog + Seq, OpenTelemetry (traces/metrics).

```
[Client (Web/PWA/Mobile)]
        │
        ▼
[API Gateway/YARP]
  ├─▶ Listings.API
  ├─▶ Search.API
  ├─▶ Payments.API ──▶ (Events)
  ├─▶ Notices.API  ──▶ (Events)
  ├─▶ Complaints.API ─▶ PdfGen.Worker → ObjectStore
  └─▶ Identity.API
       │
       └─▶ PostgreSQL / Redis / Object Storage / Message Bus / Seq
```

---

## 5) .NET Solution Layout (proposed)

```
OpenRoof.sln
├─ src/
│  ├─ Gateway/
│  │  └─ OpenRoof.ApiGateway (YARP, rate limiting, auth)
│  ├─ Identity/
│  │  ├─ OpenRoof.Identity.API
│  │  ├─ OpenRoof.Identity.Application
│  │  ├─ OpenRoof.Identity.Domain
│  │  └─ OpenRoof.Identity.Infrastructure
│  ├─ Listings/
│  │  ├─ OpenRoof.Listings.API
│  │  ├─ OpenRoof.Listings.Application
│  │  ├─ OpenRoof.Listings.Domain
│  │  └─ OpenRoof.Listings.Infrastructure
│  ├─ Search/
│  │  ├─ OpenRoof.Search.API
│  │  └─ OpenRoof.Search.Infrastructure (ES/Meilisearch adapters)
│  ├─ Payments/
│  │  ├─ OpenRoof.Payments.API
│  │  ├─ OpenRoof.Payments.Application
│  │  ├─ OpenRoof.Payments.Domain
│  │  └─ OpenRoof.Payments.Infrastructure
│  ├─ Notices/
│  │  ├─ OpenRoof.Notices.API
│  │  ├─ OpenRoof.Notices.Application
│  │  ├─ OpenRoof.Notices.Domain
│  │  └─ OpenRoof.Notices.Infrastructure
│  ├─ Complaints/
│  │  ├─ OpenRoof.Complaints.API
│  │  ├─ OpenRoof.Complaints.Application
│  │  ├─ OpenRoof.Complaints.Domain
│  │  └─ OpenRoof.Complaints.Infrastructure
│  ├─ PdfGen/
│  │  ├─ OpenRoof.PdfGen.Worker (QuestPDF/iText7, SHA‑256 signer)
│  │  └─ OpenRoof.PdfGen.Contracts
│  ├─ IoT/
│  │  ├─ OpenRoof.IoTGateway.API (MQTT/Webhooks)
│  │  └─ OpenRoof.IoTGateway.Worker
│  ├─ Shared/
│  │  ├─ OpenRoof.Shared.Abstractions (Result pattern, MediatR, exceptions)
│  │  ├─ OpenRoof.Shared.Infrastructure (EFCore, Dapper, Serilog, caching, outbox)
│  │  └─ OpenRoof.Shared.Messaging (bus adapters, contracts)
│  └─ Admin/
│     └─ OpenRoof.Admin.API (moderation, exports)
├─ tests/
│  ├─ OpenRoof.UnitTests (xUnit, FluentAssertions)
│  ├─ OpenRoof.IntegrationTests (WebApplicationFactory)
│  └─ OpenRoof.ContractTests (HTTP contract via Verify)
└─ tools/
   ├─ scripts/ (migrations, seed, dev convenience)
   └─ docker/ (compose for dev: db, bus, seq)
```

**Layering per service**: `API` (endpoints) → `Application` (CQRS, validators) → `Domain` (entities, aggregates) → `Infrastructure` (EF Core, repositories, outbox, external adapters).

---

## 6) Frontend (React + TypeScript + Vite)

```
apps/web
├─ src/
│  ├─ app/ (routing, providers, RBAC guards)
│  ├─ features/
│  │  ├─ listings/  (create/edit, detail, map)
│  │  ├─ search/    (filters, saved searches, alerts)
│  │  ├─ buySell/   (offers, negotiation)
│  │  ├─ rent/      (applications, lease terms)
│  │  ├─ payments/  (history, receipts)
│  │  ├─ notices/   (wizard, timeline)
│  │  ├─ complaints/(wizard, evidence, PDF preview)
│  │  ├─ iot/       (device panel)
│  │  └─ admin/     (moderation, KYC)
│  ├─ components/ (forms, tables, uploaders)
│  ├─ services/   (typed API clients, React Query)
│  ├─ store/      (Zustand/Redux)
│  └─ styles/
└─ e2e/ (Playwright)
```

---

## 7) Minimal Data Model (delta for new features)

* `Payments(Id, TenantId, LeaseId, Amount, DueDate, PaidOn, Status, TxnRef)`
* `Notices(Id, LeaseId, Type[Vacate/Warning], StartDate, EndDate, Status)`
* `Complaints(Id, RaisedBy[Tenant/Owner], AgainstPartyId, LeaseId, ViolationType, Description, IncidentDate, Severity, Status)`
* `ComplaintEvidence(Id, ComplaintId, Url, Kind[Image/PDF/Text], Hash)`
* `PdfAudit(Id, EntityType, EntityId, FileUrl, CreatedAt, SignatureHash)`
* `IoTEvents(Id, DeviceId, LeaseId, EventType[Scan/Pay/Notice], PayloadJson, CreatedAt)`

---

## 8) API Snapshot (v1)

* `POST /v1/listings` `GET /v1/listings?filters` `GET /v1/listings/{id}`
* `POST /v1/offers` `POST /v1/offers/{id}/counter`
* `POST /v1/payments/record` `GET /v1/leases/{id}/payments`
* `POST /v1/notices/start` `GET /v1/leases/{id}/notices`
* `POST /v1/complaints` `POST /v1/complaints/{id}/evidence`
* `POST /v1/complaints/{id}/pdf` `GET /v1/complaints/{id}`
* `POST /v1/iot/events` (MQTT bridge)

**Standards**: ProblemDetails, pagination (cursor), Idempotency‑Key, correlation IDs, ETags, OpenAPI/Swagger.

---

## 9) Non‑Functional Requirements

* **Security:** OAuth2/OIDC, per‑tenant RBAC, input validation, audit logs.
* **Privacy:** PII minimization; consented sharing; redact when externally shared.
* **Reliability:** Outbox/inbox, retries, timeouts, circuit breakers.
* **Performance:** Query projections, indexes, cache, async I/O.
* **Compliance:** PDFs carry disclaimer; no auto‑lock enforcement via IoT.

---

## 10) Roadmap (phased)

**Phase 1 (MVP):** Listings, search/filters, owner & tenant flows, chat lite, rent logs, notices, complaint → PDF.

**Phase 2:** Payments automation, KYC, agreement builder, maintenance tickets, reviews.

**Phase 3:** Broker workspace, fraud signals, analytics dashboards, IoT panel, escrow (market‑dependent).

**Phase 4:** React Native app, AI recommendations, price/rent estimates.

---

## 11) Dev Quickstart

* **Dev stack:** .NET 8, PostgreSQL, Redis, RabbitMQ, Seq, Vite + React + TS.
* `docker compose up -d` (db, redis, rabbit, seq)
* `dotnet build` → `dotnet run -p src/Gateway/OpenRoof.ApiGateway`
* Web: `pnpm i && pnpm dev` in `apps/web`.

---

## 12) Contribution Guide (short)

* **Branches:** `main` (protected), `feat/*`, `fix/*`.
* **PR checks:** build + tests + lint + stylecop + API contract.
* **Commits:** Conventional Commits (`feat:`, `fix:` …).
* **Code style:** analyzers, nullability on, async suffix, Result pattern.

---

## 13) Testing Strategy

* **Unit:** domain rules, validators.
* **Integration:** API + DB (Testcontainers).
* **Contract:** OpenAPI snapshot + Verify.
* **E2E:** Playwright for key journeys (search → apply → notice → complaint PDF).

---

## 14) Security & Legal Notes

* All complaint PDFs include a **disclaimer** and **signature hash**; externally shared versions redact PII.
* IoT device is **display/log‑only** (no lockouts); any enforcement remains human/legal.

---

## 15) License & Credits

* Open source license: MIT/Apache‑2.0 (choose one).
* "Built with ❤️ by the OpenRoof community."
