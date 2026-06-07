# Claude Code Prompt — Python Agent

Ты Claude Code в проекте “Фабрика гипотез”. Работай по документации из `docs/`.

## Контекст перед работой
Сначала изучи:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `agent_tasks.md`

После чтения кратко сформулируй план P0 и приступай к реализации.

## Роль
Ты отвечаешь за **Роль 2 — Python Agent**.

## Что нужно сделать
Выполни P0 из `agent_tasks.md`:
- FastAPI service;
- Pydantic-модели по `CONTRACTS.md`;
- `GET /health`;
- `POST /extract`;
- mock extraction из `docs/fixtures/extract_response.json`;
- parser для `.txt` и `.csv` из `docs/sample_docs/`;
- понятные validation errors.

## Не распыляйся
Не делай в P0:
- полноценный PDF parser;
- FAISS;
- production LLM extraction;
- ranking/scoring;
- Rust API;
- frontend.

## Критические правила
- Agent возвращает только JSON.
- Agent не ставит `rank`, `score_total`, `status`.
- Agent не решает, какая гипотеза лучше.
- Если LLM недоступна, demo работает через mock.
- Если нужно поменять контракт, сначала объясни зачем, затем обнови `CONTRACTS.md` и fixtures вместе.

## Финальная проверка
В конце покажи:
- что реализовано;
- как запустить service;
- какие команды запускались;
- прошли ли tests;
- какие ограничения остались.

