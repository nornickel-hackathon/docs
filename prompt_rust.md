# prompt_rust.md — Prompt для Rust Track

Ты работаешь в проекте "Фабрика гипотез". Твоя роль: **Роль 1 — Rust Track**.

Твоя зона ответственности:
- `crates/contracts` — единые типы данных + генерация TS-типов;
- `crates/engine` — детерминированный discovery engine;
- `crates/platform` — Axum HTTP API.

## Что прочитать сначала
Прочитай эти файлы из папки `docs/`:
1. `DEMO_SCENARIO.md` — что показываем на хакатоне
2. `CONTRACTS.md` — единый контракт всех типов и эндпоинтов
3. `AGENT_RULES.md` — жёсткие запреты (особенно §1 и §2)
4. `docs/WORKED_EXAMPLE.md` — конкретный пример графа для первого теста
5. `docs/SCORING.md` — формулы scoring
6. `rust_tasks.md` — твои задачи P0 и P1

## Главная цель
Сделать P0 Rust-контур: типы → engine → API против fixtures.

## Делай только P0
Не делай пока:
- Tauri, SQLite, FAISS, snapshot store;
- production LLM integration;
- P1 задачи пока P0 не готов.

## P0 задачи

### 1. Rust workspace + contracts
Создай `Cargo.toml` workspace с тремя крейтами. В `crates/contracts` опиши типы из `CONTRACTS.md`:
- `ExtractRequest`, `ExtractResponse`, `DocumentRef`
- `Claim`, `GraphNode`, `GraphEdge`
- `KpiContract`, `ScoreBreakdown`, `DoePlan`, `Hypothesis`, `BoardResponse`
- `DomainPack`, `RerunAction`
- `pub type Graph = petgraph::DiGraph<GraphNode, GraphEdge>;`

Подключи `ts-rs`, сгенерируй `web/src/contracts.ts` (нужен фронту).

### 2. Engine
В `crates/engine` реализуй:

```rust
pub fn discover(graph: &Graph, contract: &KpiContract, pack: &DomainPack) -> BoardResponse
```

- Оператор `mechanism_path`: incoming рёбра `mechanism`/`proxy` от KPI-узла до tag `controllable`
- Оператор `substitution`: рёбра `substitution` как альтернативный path
- Scoring по формулам из `docs/SCORING.md` (веса из `pack.scoring_weights`)
- Hard constraints: нарушение → `rejected_by_constraints`; uncovered constraint → `needs_expert_review`
- Сборка `Hypothesis { trace, score_breakdown, status, doe_plan }`

Стартовая тестовая фикстура — граф из `docs/WORKED_EXAMPLE.md`, захардкоженный в тест.

### 3. Platform (Axum API)
В `crates/platform`:
- `GET /board?run_id=` → `fixtures/board.json` (сначала), потом engine
- `GET /hypothesis/:id` → один `Hypothesis`
- `POST /run` → `{ snapshot_id, kpi_contract, pack_id }` → `BoardResponse`
- `POST /rerun` → `{ run_id, action: RerunAction }` → `BoardResponse`
- `POST /ingest` → `{ folder, pack_id }` → `{ job_id }` (stub)
- CORS для Vite (порт 5173)

## Данные для разработки
Все файлы внутри `docs/`:
- `fixtures/extract_response.json` — тестовый graph input
- `fixtures/board.json` — ожидаемый output
- `packs/alloys-v1.yaml` — domain pack

## Жёсткие правила
- `crates/engine` — ноль доменных слов (`aluminum`, `scandium`, `aging`, `strength` и т.п.)
- Engine не вызывает LLM и не делает I/O
- Python Agent не нужен для P0: работать против fixtures
- Менять JSON-контракт → обновить `CONTRACTS.md` + регенерить `contracts.ts` в том же коммите

## Done
- `cargo test` зелёный, включая golden-тест на fixtures
- `GET /board` возвращает валидный `BoardResponse`
- `POST /rerun` меняет ranking без повторного extraction
- `crates/engine` grep чистый от доменных слов
