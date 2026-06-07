# agent_tasks.md — Роль 2: Python Agent

## Цель роли
Сделать тонкий AI/NLP слой, который превращает документы в структурированный graph input:
`claims`, `entities`, `edges`.

Agent не ранжирует гипотезы и не ставит финальные статусы.

## Входы
- `CONTRACTS.md` — схема `ExtractResponse`.
- `fixtures/extract_response.json` — эталонный ответ.
- `sample_docs/` — тестовый корпус.
- `packs/alloys-v1.yaml` — units, tags, extraction hints.

## P0 задачи
1. Создать `sidecar` как FastAPI service.
2. Описать Pydantic-модели:
   - `DocumentRef`;
   - `Claim`;
   - `GraphNode`;
   - `GraphEdge`;
   - `ExtractResponse`.
3. Реализовать `GET /health` → `{ "status": "ok" }`.
4. Реализовать `POST /extract`:
   - принимает `{ "docs": [...], "pack_id": "alloys-v1" }`;
   - для MVP возвращает `fixtures/extract_response.json`.
5. Добавить parser для `.txt` и `.csv` из `sample_docs/`.
6. Добавить validation: если ответ не соответствует Pydantic-схеме, вернуть понятную ошибку.

## P1 задачи
1. Добавить optional LLM extraction за env flag.
2. Добавить prompt, который просит LLM вернуть только JSON по Pydantic-схеме.
3. Добавить простую нормализацию units из `packs/alloys-v1.yaml`.
4. Добавить `POST /narrate` для короткого объяснения гипотезы.
5. Добавить `POST /skeptic` для рисков и missing evidence, но без изменения ranking.

## Done
- `POST /extract` работает без LLM через mock fixture.
- Ответ содержит только валидные `claims`, `entities`, `edges`.
- Все `edge.source_claims` ссылаются на существующие claims.
- Все `edge.src` / `edge.dst` ссылаются на существующие entities.
- Агент не принимает решений о `rank`, `score_total`, `status`.

