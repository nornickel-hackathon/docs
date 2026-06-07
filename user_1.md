# user_1.md — Prompt для Rust Track

Ты работаешь в проекте “Фабрика гипотез”. Твоя роль: **Роль 1 — Rust Track**.

Твоя зона ответственности:
- `crates/contracts`;
- `crates/engine`;
- `crates/platform`.

## Что прочитать сначала
Прочитай эти файлы из папки `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `rust_tasks.md`

## Главная цель
Сделать P0 Rust-контур MVP:
- общий Rust workspace;
- типы контрактов;
- deterministic engine;
- простой Axum API;
- работа против fixtures.

## Делай только P0
Не делай пока:
- Tauri;
- SQLite;
- FAISS;
- полноценный snapshot store;
- production LLM integration;
- сложный frontend;
- P1 задачи, если P0 ещё не готов.

## P0 задачи
1. Создай Rust workspace.
2. Создай `crates/contracts`.
3. Опиши Rust-типы из `CONTRACTS.md`:
   - `KpiContract`;
   - `Claim`;
   - `GraphNode`;
   - `GraphEdge`;
   - `ScoreBreakdown`;
   - `Hypothesis`;
   - `BoardResponse`;
   - `DoePlan`;
   - `ExtractResponse`.
4. Создай `crates/engine`.
5. Реализуй публичную функцию:

```rust
pub fn discover(graph: &Graph, contract: &KpiContract, pack: &DomainPack) -> BoardResponse
```

6. Реализуй минимальные operators:
   - `mechanism_path`;
   - `substitution`;
   - `gap`.
7. Реализуй scoring:
   - `kpi_impact`;
   - `evidence`;
   - `plausibility`;
   - `cost`;
   - `risk`;
   - `novelty`.
8. Реализуй hard constraints:
   - нарушение `cost <= 5%` даёт `rejected_by_constraints`;
   - непокрытая `ductility_loss` даёт `needs_expert_review` или risk flag.
9. Создай `crates/platform` с Axum API:
   - `GET /board`;
   - `GET /hypothesis/:id`;
   - `POST /run`;
   - `POST /rerun`.
10. На первом этапе `GET /board` может отдавать `fixtures/board.json`.

## Данные для разработки
Используй:
- `fixtures/extract_response.json`;
- `fixtures/board.json`;
- `packs/alloys-v1.yaml`.

Эти файлы лежат внутри `docs/`.

## Жёсткие правила
- Не меняй JSON-контракт без обновления `CONTRACTS.md` и fixtures.
- В `crates/engine` не должно быть доменных слов вроде `aluminum`, `scandium`, `aging`, `strength`.
- Engine не должен вызывать LLM.
- Python Agent не нужен для P0: работай против fixtures.
- Frontend не нужен для P0: API достаточно проверить через JSON/curl/tests.

## Done
Работа готова, когда:
- Rust-типы парсят `fixtures/extract_response.json`;
- Rust-типы парсят `fixtures/board.json`;
- `GET /board` возвращает валидный `BoardResponse`;
- `POST /run` возвращает стабильный ranking;
- `POST /rerun` меняет ranking/status без повторного extraction;
- tests проходят;
- engine остаётся generic и без доменной семантики.

