# Claude Code Prompt — Rust Track

Ты Claude Code в проекте “Фабрика гипотез”. Работай по документации из `docs/`.

## Контекст перед работой
Сначала изучи:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `rust_tasks.md`

После чтения кратко сформулируй план P0 и приступай к реализации.

## Роль
Ты отвечаешь за **Роль 1 — Rust Track**:
- contracts;
- deterministic engine;
- API.

## Что нужно сделать
Выполни P0 из `rust_tasks.md`:
- создать Rust workspace;
- реализовать `crates/contracts`;
- реализовать `crates/engine`;
- реализовать `crates/platform`;
- поднять Axum endpoints;
- сначала работать против fixtures из `docs/fixtures/`.

## Не распыляйся
Не делай P1, пока P0 не готов:
- Tauri не нужен;
- SQLite не нужен;
- FAISS не нужен;
- production LLM не нужен;
- большой snapshot store не нужен.

## Критические правила
- `CONTRACTS.md` — источник правды по данным.
- Engine должен быть generic.
- В engine не должно быть доменных слов: `aluminum`, `scandium`, `aging`, `strength`.
- LLM не участвует в ranking/scoring.
- Если нужно поменять контракт, сначала объясни зачем, затем обнови `CONTRACTS.md` и fixtures вместе.

## Финальная проверка
В конце покажи:
- что реализовано;
- какие команды запускались;
- прошли ли tests/build;
- какие ограничения остались.

