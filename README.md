# 🌐 VedaSphere — Multi-Language City Brief & Advisory Monorepo

**A trusted, single-screen daily city briefing platform for Indian families, translating raw civic and environmental data into actionable guidance in English, Hindi, and Telugu.**

[![Architecture: Turborepo Monorepo](https://img.shields.io/badge/Architecture-Turborepo_Monorepo-ef4444?style=for-the-badge&logo=turborepo&logoColor=white)](package.json)
[![Frontend: Next.js 15](https://img.shields.io/badge/Frontend-Next.js_App_Router-black?style=for-the-badge&logo=next.js&logoColor=white)](apps/web)
[![Backend: Fastify Node 20](https://img.shields.io/badge/Backend-Fastify_·_TypeScript_·_Zod-000000?style=for-the-badge&logo=fastify&logoColor=white)](apps/api)
[![Compliance: DPDP Act 2023](https://img.shields.io/badge/Privacy-DPDP_Act_2023_Compliant-10b981?style=for-the-badge)](README.md)

> ⚠️ **Development Status & Data Source Disclosure:**  
> VedaSphere is currently an **engineering prototype in active development**.  
> The codebase implements the core Turborepo monorepo structure, shared contract schemas (`@vedasphere/shared`), Fastify API gateway with LRU caching, and Next.js PWA web interface.  
> Third-party civic feeds (live AQI, CPCB sensors, fuel feeds, mandi commodity APIs) currently operate under a graceful fallback tier: **Fresh Live API $\rightarrow$ Stale Cache $\rightarrow$ Demo Seed Data**.

---

## 1. Product Vision & Design Principles

Urban and semi-urban Indian citizens are inundated with noisy news apps, intrusive advertisements, and complex data charts. A resident checking the air quality or weather does not need complex meteorological graphs; they need clear, localized meaning:
- *Instead of "AQI 184 (PM2.5: 88µg/m³)" $\rightarrow$ **"AQI 184 (Moderate) — wear a mask during morning walks."***
- *Instead of raw fuel price changes $\rightarrow$ **"Petrol up ₹0.35/L in Vijayawada today."***

### Core Principles
1. **Meaning Over Raw Numbers:** Every metric is accompanied by a plain-language actionable recommendation.
2. **Attribution & Timestamps:** Every card explicitly credits its source agency with an "Updated X min ago" badge.
3. **Graceful Fallback:** If upstream civic APIs experience outages, the UI degrades gracefully: **Fresh** $\rightarrow$ **Stale** $\rightarrow$ **Demo**.
4. **Sub-3s 4G Performance:** Lightweight assets designed to render in under 3 seconds on modest mobile devices.
5. **Calm & Ad-Free:** Zero third-party advertising, zero push notification spam, zero speculative trading tips.
6. **Privacy-First (DPDP Act 2023):** Zero mandatory location tracking; city selection is manual or user-permitted; instant data deletion.

---

## 2. Monorepo Architecture

VedaSphere is organized as a high-velocity TypeScript monorepo powered by **Turborepo** and **pnpm workspaces**:

```
veda-sphere/
├── apps/
│   ├── web/               # Next.js App Router PWA with Tailwind CSS & i18n
│   └── api/               # Fastify API Gateway (Node 20+, Zod, Pino, Helmet, LRU cache)
├── packages/
│   ├── shared/            # Shared Zod schemas, TypeScript interfaces, contracts
│   ├── ui/                # Shared UI component primitives
│   └── config/            # Shared ESLint, Prettier, and TypeScript configs
├── docs/                  # Architecture decision records (ADRs)
└── docker-compose.yml     # Local orchestration for API, Redis, and Web
```

```mermaid
graph TD
    Client["`apps/web`<br/>(Next.js App Router · Tailwind CSS · i18n: en/hi/te)"]
    SharedPkg["`packages/shared`<br/>(Zod Schemas, Domain Models, Contract Types)"]
    
    subgraph Fastify_Backend["`apps/api` (Node 20+ Fastify API Gateway)"]
        Guard["Helmet & Rate Limiting Middleware"]
        Cache["In-Memory LRU Cache & Redis Connector"]
        CityService["City Brief Aggregation Service"]
        AQIFetcher["AQI & Environmental Adapter"]
        WeatherFetcher["Weather & Advisory Adapter"]
    end

    Client <-->|Shared Types & Contracts| SharedPkg
    Fastify_Backend <-->|Shared Types & Validation| SharedPkg
    Client -->|HTTPS REST| Guard
    Guard --> Cache
    Cache --> CityService
    CityService --> AQIFetcher
    CityService --> WeatherFetcher
```

---

## 3. Technology Stack Breakdown

| Layer | Technology | Architectural Role |
|---|---|---|
| **Monorepo Engine** | Turborepo, pnpm | Cached incremental builds, workspace dependency linking, fast task pipelines. |
| **Web Frontend** | Next.js 15 (App Router), React 19, Tailwind CSS, Lucide | Responsive mobile-first PWA, multi-language localization (English, Hindi, Telugu). |
| **API Gateway** | Fastify (Node 20+), TypeScript | Low-overhead asynchronous HTTP server, input validation via Zod, Pino logging. |
| **Shared Contracts** | Zod, TypeScript | End-to-end type safety ensuring frontend and API agree on payload contracts. |
| **Caching Layer** | In-Memory LRU Cache, Redis (Docker Compose) | Throttles upstream third-party calls and serves instantaneous cached city snapshots. |
| **Containerization** | Docker, Docker Compose | Reproducible local microservices orchestration. |

---

## 4. Current Implementation Status vs. Roadmap

| Domain Module | Implemented in Codebase | Roadmap / Planned Integration |
|---|---|---|
| **City Brief Engine** | Core aggregator, fallback tiers, card templates | Real-time push websocket updates |
| **AQI & Air Quality** | Schemas, advisory parser, demo mock feed | CPCB / OpenAQ live sensor ingest |
| **Weather & Rains** | Weather card UI, heatwave advisory rules | IMD (India Meteorological Dept) API |
| **Fuel & Energy** | Data models for Petrol/Diesel rates | Automated daily oil marketing company scrapers |
| **Localization** | Multi-lingual UI dictionary (en, hi, te) | Contextual regional dialect translations |

---

## 5. Local Setup & Execution

### Prerequisites
- Node.js 20+
- pnpm (recommended) or npm
- Docker (optional)

### 1. Clone & Install
```bash
git clone https://github.com/Balama2520/veda-sphere.git
cd veda-sphere

# Install workspace dependencies
npm install
```

### 2. Environment Configuration
```bash
cp .env.example .env
```

### 3. Run Development Monorepo
```bash
# Concurrently start web (port 3000) and api (port 4000)
npm run dev
```

- Web Portal: `http://localhost:3000`
- Fastify API: `http://localhost:4000`

---

## 6. Author & License

- **Architect & Lead Engineer:** Bala Maneesh Ayanala ([@Balama2520](https://github.com/Balama2520))
- **Portfolio:** [abms-portfolio.netlify.app](https://abms-portfolio.netlify.app)
- **License:** Released under the [MIT License](LICENSE).
