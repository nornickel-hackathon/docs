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
- парсинг `txt/html/pdf/csv` в текст;
- **Entity Resolver**: нормализация синонимов материалов/процессов (Al-7075 = AA7075 = Aluminum 7075) через rules + fuzzy matching + LLM judge; неуверенные кластеры — в очередь эксперту;
- LLM extraction: claims по Pydantic-схеме, confidence ≥ 0 установлен LLM;
- mock extraction для стабильного демо (возвращает `fixtures/extract_response.json`);
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

## Три механизма генерации гипотез (engine)

**Gap-based** — оператор `gap`: factor A связан с KPI, factor B связан с KPI, но комбинация A+B в corpus не проверялась → гипотеза «проверить A+B».

**Contradiction-based** — оператор `contradiction`: два claim с противоположным polarity на одном edge при разных conditions → гипотеза «определить граничное условие, при котором эффект меняет знак».

**Analogy-based** — оператор `analogy_transfer`: в узле X есть подтверждённый mechanism-path до KPI; узел Y имеет совпадающие теги без покрытого пути → гипотеза «перенести механизм X на Y».

Все три оператора работают только над `EdgeType` и тегами узлов. Никаких доменных слов в коде.

## Принцип независимой разработки
Все компоненты разрабатываются против fixtures и контрактов.

Если компонент умеет читать и отдавать согласованный JSON, он совместим с остальными частями.
Интеграция не должна ждать “идеального” extraction или финального UI.

