# Codex Prompt — Rust Track

Ты Codex в проекте “Фабрика гипотез”. Работай внутри репозитория разработки, используя папку `docs/` как источник правды.

## Сначала прочитай
Из `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `rust_tasks.md`

## Роль
Ты выполняешь **Роль 1 — Rust Track**:
- `crates/contracts`;
- `crates/engine`;
- `crates/platform`.

## Задача
Реализуй только P0 из `rust_tasks.md`.

Сделай:
- Rust workspace;
- `crates/contracts` с типами из `CONTRACTS.md`;
- `crates/engine` с `discover(graph, contract, pack)`;
- `crates/platform` с Axum API;
- чтение/валидацию fixtures;
- `GET /board`, `GET /hypothesis/:id`, `POST /run`, `POST /rerun`.

## Ограничения
- Не делай Tauri, SQLite, FAISS, production LLM, сложный snapshot store.
- Не меняй JSON-контракт без одновременного обновления `CONTRACTS.md` и fixtures.
- В `crates/engine` не должно быть доменных слов вроде `aluminum`, `scandium`, `aging`, `strength`.
- Engine не вызывает LLM и не зависит от Python.
- Сначала работай против `docs/fixtures/board.json`, `docs/fixtures/extract_response.json`, `docs/packs/alloys-v1.yaml`.

## Проверки
Перед финалом проверь:
- fixtures парсятся;
- API отдаёт валидный `BoardResponse`;
- engine даёт стабильный результат на одинаковом input;
- tests/build проходят или явно напиши, что не удалось запустить и почему.

