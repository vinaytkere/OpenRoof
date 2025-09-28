# Open Roof, A Real Estate App 🏡

> Transparent, low‑cost, open‑source real estate platform built with modern Microsoft‑aligned technologies, DSA‑driven algorithms, and best practices for scalability.

---

## 🚀 Tech Stack

**Backend**

* ASP.NET Core 9 (Minimal APIs, CQRS via MediatR)
* EF Core 9 + PostgreSQL 17
* FluentValidation, Ardalis.Specification
* Redis (caching), Azure Blob (file storage)
* Authentication: Identity + JWT (upgrade path to Microsoft Entra B2C)
* Observability: OpenTelemetry + Grafana/Jaeger/Prometheus

**Frontend**

* React 18+, TypeScript 5.6+, Vite
* TailwindCSS + shadcn/ui, Lucide icons
* TanStack Query, React Hook Form + Zod

**DevOps & Quality**

* Docker + Compose (K8s/Helm later)
* GitHub Actions, CodeQL, Dependabot, SonarCloud
* Testing: xUnit, Verify, Respawn, Playwright

---

## 🎯 MVP Features

* Property listings (create, search, filter, details)
* Buyer / Seller / Broker roles
* Transparent broker commission display
* Secure login & registration
* Inquiry system (buyer → seller/broker)

---

## 📂 Repo Structure

```
src/
  Server/
    OpenRoof.Api/
    OpenRoof.Application/
    OpenRoof.Domain/
    OpenRoof.Infrastructure/
    OpenRoof.Algorithms/   # DSA library (Trie, BK‑Tree, Kd‑Tree, etc.)
  Web/
    realestate-web/          # React + TS app
tests/
deploy/
docs/
```

---

## 🧮 DSA Integration Highlights

* **Trie** → Autocomplete for locality/amenities
* **BK‑Tree** → Fuzzy search (typo‑tolerant)
* **Kd‑Tree (2D)** → Nearby property lookup
* **Priority Queue** → Ranking feeds
* **Interval Scheduling** → Broker availability & open houses
* **MinHash + LSH** → Duplicate property detection
* **LRU/LFU** → Local caching beside Redis
* **Token Bucket** → API rate limiting

---

## ▶️ Quick Start

### Prerequisites

* .NET 9 SDK
* Node.js 20+ & pnpm/npm
* Docker & Docker Compose

### Run locally

```bash
# Clone repo
git clone https://github.com/your-username/real-estate-app.git
cd real-estate-app

# Start services
docker compose up -d

# Backend
dotnet run --project src/Server/OpenRoof.Api

# Frontend
cd src/Web/realestate-web
npm install
npm run dev
```

App will be available at: [http://localhost:5173](http://localhost:5173)

---

## 📖 Documentation

* [Full Architecture & Blueprint](./docs/blueprint.md)
* [DSA Integration Plan](./docs/algorithms.md)

---

## ✅ Roadmap (MVP)

*

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

### 👤 Maintainer

Built with ❤️ by VTG
