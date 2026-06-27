# prompt_python.md — Prompt для Python Agent

Ты работаешь в проекте "Фабрика гипотез". Твоя роль: **Роль 2 — Python Agent**.

Твоя зона ответственности: `sidecar/` — extraction pipeline, Pydantic-схемы, FastAPI сервис.

## Что прочитать сначала
1. `DEMO_SCENARIO.md` — что показываем на хакатоне
2. `CONTRACTS.md` — все типы (особенно `ExtractRequest`, `ExtractResponse`, `Claim`, enum `evidence_type`)
3. `AGENT_RULES.md` — жёсткие правила (§2: не PyO3, §3: LLM не ранжирует)
4. `python_tasks.md` — задачи P0/P1, схема входа, enum evidence_type, prompt-шаблон LLM

## Главная цель
P0 Python сайдкар (порт 8765), который отдаёт валидный `ExtractResponse` для Rust Track.

Agent не ранжирует гипотезы и не ставит финальные статусы.

## Делай только P0
Не делай пока:
- PDF parser (только `.txt` и `.csv` в P0)
- FAISS / Qdrant / Chroma
- LLM extraction (за env flag `SIDECAR_LLM_ENABLED=true` — P1)
- Entity Resolver на embeddings (базовый словарный — P0, расширенный — P1)
- ranking, scoring, status
- frontend или Rust-код

## P0 задачи
1. `sidecar/` — FastAPI service, порт 8765
2. `GET /health` → `{ "status": "ok" }`
3. Pydantic-модели входа (`DocInput`, `ExtractRequest`) и выхода (`Claim`, `GraphNode`, `GraphEdge`, `ExtractResponse`) — схемы в `CONTRACTS.md`
4. `POST /extract` → для MVP возвращает `fixtures/extract_response.json` без парсинга
5. Parser `.txt` (абзацы) и `.csv` (pandas → experiment claims)
6. Базовый Entity Resolver: нормализация через словарь из `packs/alloys-v1.yaml`
7. Validation: невалидный output → `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] } }` HTTP 422

## Данные для разработки (все в `docs/`)
- `fixtures/extract_response.json` — эталонный mock-ответ
- `sample_docs/aging_2618a.txt`, `sample_docs/sc_zr_notes.txt`
- `sample_docs/internal_experiments.csv`
- `packs/alloys-v1.yaml`

## Жёсткие правила
- Только JSON на выходе; никаких `rank`, `score_total`, `status`
- LLM недоступна → demo работает через mock
- Менять контракт → обновить `CONTRACTS.md` и fixtures

## Done
- `GET /health` → ok
- `POST /extract` → валидный `ExtractResponse` из mock
- Все `edge.source_claims` ссылаются на существующие `claim.id`
- Все `edge.src`/`edge.dst` ссылаются на существующие `entity.id`
- Невалидный input → понятная ошибка
- Нет логики ranking/scoring
