# user_2.md — Prompt для Python Agent

Ты работаешь в проекте “Фабрика гипотез”. Твоя роль: **Роль 2 — Python Agent**.

Твоя зона ответственности:
- `sidecar`;
- extraction pipeline;
- Pydantic-схемы;
- mock/fixture extraction;
- optional LLM extraction позже.

## Что прочитать сначала
Прочитай эти файлы из папки `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `AGENT_RULES.md`
6. `agent_tasks.md`

## Главная цель
Сделать P0 Python Agent, который отдаёт валидный `ExtractResponse` для Rust Track.

Agent не ранжирует гипотезы и не ставит финальные статусы.

## Делай только P0
Не делай пока:
- сложный PDF parser;
- FAISS;
- production LLM pipeline;
- ranking;
- scoring;
- frontend;
- Rust-код.

## P0 задачи
1. Создай `sidecar` как FastAPI service.
2. Добавь `GET /health`:

```json
{ "status": "ok" }
```

3. Опиши Pydantic-модели из `CONTRACTS.md`:
   - `DocumentRef`;
   - `Claim`;
   - `GraphNode`;
   - `GraphEdge`;
   - `ExtractResponse`.
4. Реализуй `POST /extract`.
5. Для MVP endpoint может возвращать `fixtures/extract_response.json`.
6. Добавь parser для `.txt` и `.csv` из `sample_docs/`.
7. Добавь validation:
   - если fixture или LLM output не проходит Pydantic-схему, вернуть понятную ошибку.

## Данные для разработки
Используй:
- `fixtures/extract_response.json`;
- `sample_docs/aging_2618a.txt`;
- `sample_docs/sc_zr_notes.txt`;
- `sample_docs/internal_experiments.csv`;
- `packs/alloys-v1.yaml`.

Эти файлы лежат внутри `docs/`.

## Жёсткие правила
- Agent отдаёт только JSON.
- Agent не делает `rank`, `score_total`, `status`.
- Agent не принимает решение, какая гипотеза лучше.
- Если LLM недоступна, demo всё равно должно работать через mock extraction.
- Не меняй контракт без обновления `CONTRACTS.md` и fixtures.

## Done
Работа готова, когда:
- `GET /health` возвращает ok;
- `POST /extract` возвращает валидный `ExtractResponse`;
- все `edge.source_claims` ссылаются на существующие claims;
- все `edge.src` / `edge.dst` ссылаются на существующие entities;
- невалидный input даёт понятную ошибку;
- Agent не содержит логики ranking/scoring.

