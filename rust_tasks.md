# rust_tasks.md — Роль 1: Rust Track

## Цель роли
Собрать весь Rust-контур MVP: общие контракты, deterministic engine и HTTP API для фронта.

Один разработчик владеет:
- `crates/contracts`;
- `crates/engine`;
- `crates/platform`.

## Входы
- `CONTRACTS.md` — источник JSON/Rust/TS типов.
- `fixtures/extract_response.json` — тестовый graph input.
- `fixtures/board.json` — ожидаемая форма board для API/frontend.
- `packs/alloys-v1.yaml` — веса, operators, constraints, теги.

## P0 задачи
1. Создать Rust workspace:
   - `crates/contracts`;
   - `crates/engine`;
   - `crates/platform`.
2. Описать типы из `CONTRACTS.md` в `crates/contracts`.
3. Подключить `serde`, `serde_json`, `thiserror`; для TS-экспорта позже можно добавить `ts-rs`.
4. В `crates/engine` реализовать публичную функцию:

```rust
pub fn discover(graph: &Graph, contract: &KpiContract, pack: &DomainPack) -> BoardResponse
```

5. Реализовать MVP operators:
   - `mechanism_path`: идти от KPI назад по `mechanism`/`proxy` edges до `controllable` factors;
   - `substitution`: учитывать `substitution` edges как альтернативный factor;
   - `gap`: создавать/помечать hypothesis как `needs_expert_review`, если constraint не покрыт claims.
6. Реализовать scoring:
   - `kpi_impact`;
   - `evidence`;
   - `plausibility`;
   - `cost`;
   - `risk`;
   - `novelty`.
7. Реализовать hard constraints:
   - нарушение `cost <= 5%` даёт `rejected_by_constraints`;
   - непокрытая `ductility_loss` даёт `needs_expert_review` или risk flag.
8. В `crates/platform` поднять Axum API:
   - `GET /board`;
   - `GET /hypothesis/:id`;
   - `POST /run`;
   - `POST /rerun`.
9. На первом этапе API может отдавать `fixtures/board.json`, затем подключает engine.

## P1 задачи
1. Добавить загрузку `packs/<pack_id>.yaml`.
2. Добавить deterministic snapshot hash для graph input.
3. Добавить простой in-memory run store.
4. Поддержать rerun actions:
   - `exclude_factor`;
   - `change_weight`;
   - `add_constraint`;
   - `relax_constraint`.
5. Добавить contract tests на fixtures.

## Done
- `fixtures/extract_response.json` парсится в Rust-типы.
- `fixtures/board.json` парсится в Rust-типы.
- `GET /board` отдаёт валидный `BoardResponse`.
- `POST /run` возвращает стабильный ranking.
- `POST /rerun` меняет ranking/status без повторного extraction.
- В `crates/engine` нет доменных слов вроде `aluminum`, `scandium`, `aging`, `strength`.

