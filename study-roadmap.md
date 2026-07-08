# Purple Fabric — Personal Study Roadmap

> A 12-week, trackable plan to understand the whole architecture and work independently.
> Tailored for: Foundation Layer (Java 21 / Spring Boot + JAX-RS) developer, ~1 yr experience.
> Goal: go from AI-assisted to AI-reviewed — own the code, don't lean on it.

**How to use this file:** tick boxes as you go (`- [x]`). Each phase lists exact files to read
in *this* workspace so you're never starting from a blank page. Docs-first, then source
(Core Principle #8 — Evidence-Based, Knowledge-First).

---

## Where you actually work (prioritize this)

Your tickets (FAB-9204, FAB-10899, MAP-29897, …) live in the **Foundation Layer (Fabric)**:
`platform-data-service`, `platform-email-service`, `platform-dms-service`,
`platform-document-service`, `platform-common-library`.

Stack: **Java 21 + Spring Boot 3.5.6 + JAX-RS (Jersey) + Maven + MongoDB + Redis + Vault + Keycloak + Kong**.

Priority legend: 🔴 deep · 🟠 solid working knowledge · 🟡 awareness only

---

## Phase 0 — Solidify core Java (Week 1–2) 🔴
*The phase that ends AI-dependence. Foundation code assumes strong core Java.*

- [ ] Java 21 features used here: records, sealed classes, `Optional`, streams, `var`, switch expressions, text blocks
- [ ] Concurrency basics: threads, `CompletableFuture`, blocking vs non-blocking I/O
- [ ] Collections, generics, exception design, `equals`/`hashCode`
- [ ] **Discipline drill:** for one week, read code and *predict* behavior before asking AI; write your explanation, then verify
- **Read in repo:** `.claude/knowledge/java.md`

## Phase 1 — Spring Boot + JAX-RS, the Fabric idiom (Week 3–4) 🔴
*The pattern in every service you touch.*

- [ ] Spring Boot: DI, `@Component`/`@Service`/`@Repository`, config via `application.yml` + `@Value`/`@Property`, profiles
- [ ] JAX-RS (Jersey): `*RS` resource classes **must be registered in `JerseyConfig`** or the route is dead (your own memory note)
- [ ] Why Fabric layers JAX-RS on top of Spring Boot
- [ ] Maven multi-module: how `platform-parent-svc` (parent POM) pins versions; tests skipped by default → use opt-in profile (your memory note)
- [ ] `platform-common-library`: the 10 shared modules (security filters, Redis cache, Keycloak adapter, Vault utils, DMS)
- [ ] API path convention `/idxp/{service}/magicplatform/v1/{resource}`; health `/healthz/live` + `/healthz/ready`
- **Read in repo:** `.claude/knowledge/java-fabric.md`, `docs/services/platform-parent-svc/`, `docs/services/platform-common-library/`
- **Do:** open `repos/platform-email-service` and trace one endpoint `*RS` → service → DAO → MongoDB

## Phase 2 — Data & integration layer (Week 5–6) 🔴
- [ ] MongoDB: document modeling, **tenant-scoped queries** (every query filters by workspace/tenant), indexes
- [ ] Redis: caching patterns, tenant config caching (`reconcileCache` / `updateCache` — you touched these in FAB-9204)
- [ ] Messaging: ActiveMQ/JMS queues, AWS SQS/SNS
- [ ] AWS S3 / DMS (the MAP-29897 S3 duplication bug lives here)
- **Read in repo:** `docs/databases/`, `docs/services/platform-event-service/`, `docs/services/platform-jobs-service/`, `docs/services/platform-dms-service/`, `docs/services/platform-document-service/`

## Phase 3 — Security, identity & multi-tenancy (Week 7–8) 🟠
*Where junior devs cause critical defects. Treat seriously.*

- [ ] Multi-tenant isolation: every query filtered, every endpoint auth-guarded (Core Principle #7 — non-negotiable)
- [ ] Keycloak + OAuth2/OIDC token flow; service-to-service (S2S) tokens
- [ ] Kong API Gateway: request routing + validation before your service
- [ ] HashiCorp Vault: runtime secrets; **never read `.env`** (a hook blocks it — use `dotenv`/`@Value`)
- **Read in repo:** `.claude/knowledge/tenant-isolation-patterns.md`, `docs/architecture/call-chains/chain-login-auth-flow.md`, `docs/architecture/call-chains/chain-vault-secret-resolution.md`, `docs/architecture/api-gateway.md`

## Phase 4 — The whole architecture clicks (Week 9) 🟠
- [ ] Five-plane model: Design · Runtime · Control · Knowledge · Observability
- [ ] Read 3–4 call chains touching your services
- [ ] Service dependency matrix (Core Principle #10 — cross-service impact)
- [ ] Domain terminology (Digital Expert, EDE, "MAP = Magic Platform", Asset framework)
- **Read in repo:** `docs/architecture/overview.md`, `docs/architecture/call-chains/` (start: `chain-document-ingest.md`, `chain-tenant-provisioning.md`), `docs/architecture/service-dependency-matrix.md`, `.claude/knowledge/purple-fabric-domain.md`

## Phase 5 — Infra & DevOps awareness (Week 10) 🟡
- [ ] Docker + Kubernetes basics: pods, deployments, services, config maps, health probes
- [ ] Helm: how your service deploys
- [ ] CI/CD: Jenkins flow; branch strategy `feature-dev/*` → `develop` → `Release_*` → `master`; protected branches
- [ ] Observability: Datadog, Grafana/Loki; structured log format `txn_id|tenant|workspace|user|Class=>Method|msg`
- **Read in repo:** `.claude/knowledge/helm.md`, `.claude/knowledge/deployment-process.md`, `docs/infrastructure/`

## Phase 6 — MAP/AI Layer literacy (Week 11–12) 🟡
*Breadth only — enough to collaborate and read cross-layer call chains.*

- [ ] Concepts: RAG, embeddings, vector search, LLM orchestration (LangGraph/ReAct), MCP, "everything is an Asset"
- [ ] Skim one Python/FastAPI service and one Node/NestJS service
- **Read in repo:** `docs/services/idxp-mapqueryengine-svc/`, `.claude/knowledge/python.md`, `.claude/knowledge/nodejs.md`

---

## The habit that actually makes you senior — break AI-dependence

- [ ] **Predict-then-verify:** write what you *think* is true before asking AI, then check
- [ ] **Trace one flow by hand each week:** follow a call chain through real code, no AI, draw it on paper
- [ ] **AI as reviewer, not author:** write the code yourself, then ask "what's wrong with this?"
- [ ] **Reproduce before fixing** (Core Principle #4): failing test first — you did this well on MAP-29897
- [ ] **Explain it out loud:** if you can't explain *why* JerseyConfig registration matters without notes, you don't own it yet

---

## Orientation reading (do this first, ~1 day)

- [ ] `README.md` — repo overview + `/wf-*` commands
- [ ] `AIDLC-SETUP.md` — how the AIDLC workflow you already use is set up
- [ ] `CLAUDE.md` — the 12 Core Principles + coding standards (skim, then keep as reference)
- [ ] `.claude/knowledge/repos.md` — the full service catalog + tech stack per service
- [ ] `.claude/knowledge/coding-standards.md` — mandatory rules that get flagged in review

---

*Progress: Phase ⬜0 ⬜1 ⬜2 ⬜3 ⬜4 ⬜5 ⬜6 — update as you complete each.*
