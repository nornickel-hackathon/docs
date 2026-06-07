# ARCHITECTURE.md — архитектура MVP

## Одной фразой
MVP — это объяснимая фабрика гипотез: Python извлекает факты из корпуса, Rust детерминированно
строит и ранжирует гипотезы, frontend показывает портфель и trace.

## Компоненты

### Rust Track: Contracts + Engine + API
Один Rust-разработчик владеет и ядром, и платформенным API.

Ответственность:
- `crates/contracts`: единые структуры данных;
- `crates/engine`: deterministic discovery/scoring;
- `crates/platform`: HTTP API поверх engine и fixtures;
- валидация JSON от Python Agent;
- rerun без повторного extraction.

Engine не содержит доменных слов. Слова вроде `aluminum`, `aging`, `strength`, `cost` живут
только в fixtures, sample docs и domain pack.

### Python Agent
Python Agent — волатильный слой извлечения.

Ответственность:
- парсинг `txt/html/pdf` в текст;
- mock extraction для стабильного демо;
- optional LLM extraction по Pydantic-схеме;
- endpoint `POST /extract`;
- возврат только JSON: `claims`, `entities`, `edges`.

Agent не ранжирует гипотезы и не ставит финальные статусы.

### Frontend
Frontend — демонстрационная витрина.

Ответственность:
- KPI input;
- portfolio board;
- hypothesis card;
- trace/source view;
- score breakdown;
- risks/skeptic block;
- DOE plan;
- rerun controls.

Frontend должен уметь работать от `fixtures/board.json` без backend, чтобы не блокироваться.

### Demo/Data/QA Track
Четвёртый участник владеет независимым треком данных и приёмки.

Ответственность:
- `fixtures/board.json`;
- `fixtures/extract_response.json`;
- `packs/alloys-v1.yaml`;
- `sample_docs/`;
- проверка demo flow;
- синхронизация контрактов между Rust, Python и frontend.

## Поток данных
```text
sample_docs/
  -> Python Agent /extract
  -> ExtractResponse { claims, entities, edges }
  -> Rust API validates JSON
  -> Rust Engine discover(graph, kpi_contract, domain_pack)
  -> BoardResponse { snapshot, hypotheses[] }
  -> Frontend board + hypothesis card
```

## API MVP
- `POST /extract`: proxy или мок ответа агента.
- `POST /run`: запускает discovery по corpus/snapshot и KPI.
- `GET /board`: отдаёт текущий `BoardResponse`.
- `GET /hypothesis/:id`: отдаёт одну гипотезу.
- `POST /rerun`: меняет веса/constraints/excluded factors и пересчитывает ranking.

## Принцип независимой разработки
Все компоненты разрабатываются против fixtures и контрактов.

Если компонент умеет читать и отдавать согласованный JSON, он совместим с остальными частями.
Интеграция не должна ждать “идеального” extraction или финального UI.

