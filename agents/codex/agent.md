# Codex Prompt — Python Agent

Ты Codex в проекте “Фабрика гипотез”. Работай внутри репозитория разработки, используя папку `docs/` как источник правды.

## Сначала прочитай
Из `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `python_tasks.md`

## Роль
Ты выполняешь **Роль 2 — Python Agent**.

## Задача
Реализуй только P0 из `python_tasks.md`.

Сделай:
- FastAPI service в `sidecar`;
- Pydantic-модели по `CONTRACTS.md`;
- `GET /health`;
- `POST /extract`;
- mock extraction из `docs/fixtures/extract_response.json`;
- простой parser для `.txt` и `.csv` из `docs/sample_docs/`;
- validation ошибок.

## Ограничения
- Agent не ранжирует гипотезы.
- Agent не выставляет `rank`, `score_total`, `status`.
- Не делай production LLM pipeline в P0.
- Если LLM недоступна, demo должно работать через mock extraction.
- Не меняй JSON-контракт без обновления `CONTRACTS.md` и fixtures.

## Проверки
Перед финалом проверь:
- `GET /health` возвращает ok;
- `POST /extract` возвращает валидный `ExtractResponse`;
- `edge.source_claims` указывают на существующие claims;
- `edge.src` и `edge.dst` указывают на существующие entities;
- tests проходят или явно напиши, что не удалось запустить и почему.

