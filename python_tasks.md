# python_tasks.md — Роль 2: Python Agent

## Цель роли
Сделать тонкий AI/NLP слой, который превращает документы в структурированный graph input:
`claims`, `entities`, `edges`.

Agent не ранжирует гипотезы и не ставит финальные статусы.

## Входы
- `CONTRACTS.md` — схема `ExtractResponse` и `Claim`.
- `fixtures/extract_response.json` — эталонный ответ (основа mock).
- `sample_docs/` — тестовый корпус (`.txt`, `.csv`).
- `packs/alloys-v1.yaml` — units, tags, extraction hints.
- `docs/API_CONVENTIONS.md` — схема ошибок и порт сервиса.

## Входная схема POST /extract

```python
class DocInput(BaseModel):
    path: str        # относительный путь к файлу
    mime: str        # "text/plain" | "text/csv" | "application/pdf"

class ExtractRequest(BaseModel):
    docs: List[DocInput]
    pack_id: str     # "alloys-v1"
```

Rust Platform передаёт пути, которые она сама нашла при сканировании папки. Сайдкар читает файлы по этим путям.

## Enum: evidence_type (допустимые значения в Claim)

| Значение | Когда использовать |
|----------|--------------------|
| `"literature"` | факт из научной статьи / справочника |
| `"experiment"` | факт из CSV/таблицы с экспериментами |
| `"expert_note"` | комментарий эксперта в тексте |
| `"data_gap"` | явное указание на отсутствие данных |
| `"inferred"` | LLM вывел по косвенным признакам (низкий confidence) |

## P0 задачи
1. Создать `sidecar/` как FastAPI service (порт 8765).
2. Описать Pydantic-модели:
   - `DocInput`, `ExtractRequest` (вход, см. выше);
   - `DocumentRef`, `Claim`, `GraphNode`, `GraphEdge`, `ExtractResponse` (выход по `CONTRACTS.md`).
3. Реализовать `GET /health` → `{ "status": "ok" }`.
4. Реализовать `POST /extract`:
   - принимает `ExtractRequest`;
   - для MVP возвращает `fixtures/extract_response.json` без парсинга.
5. Добавить parser для `.txt` и `.csv` (PDF — P1):
   - `.txt` → читать как plain text, разбивать на абзацы;
   - `.csv` → pandas, каждая строка — потенциальный experiment claim.
6. Добавить validation: если output не проходит `ExtractResponse` — вернуть `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] } }` с HTTP 422.
7. **Entity Resolver (базовый)**: нормализовать синонимы материалов/процессов через словарь из `packs/alloys-v1.yaml`. Если уверенность < 0.7 — оставить оригинал, не склеивать.

## P1 задачи
1. Добавить optional LLM extraction за env flag `SIDECAR_LLM_ENABLED=true`.
2. LLM prompt: system-prompt обязан включать JSON-схему Claim и пример. LLM должна возвращать `{"claims": [...]}`. Валидировать через Pydantic перед отдачей.

   ```
   Системный промпт (шаблон):
   You are a scientific claim extractor for materials science.
   Extract factual claims from the text below.
   Return ONLY valid JSON: {"claims": [{"text": "...", "confidence": 0.0-1.0, "evidence_type": "literature|experiment|expert_note|inferred"}]}
   Do not include opinions, summaries, or hypotheses.
   ```

3. Добавить `POST /embed` → `{ "vectors": [[f32]] }` для вектор-поиска (Chroma/Qdrant).
4. Добавить `POST /narrate` — принимает `Hypothesis`, возвращает `{ "text": "..." }`.
5. Добавить `POST /skeptic` — принимает `Hypothesis`, возвращает `{ "objection": "...", "missing_evidence": [...], "risks": [...] }`. Без изменения ranking.
6. Entity Resolver расширенный: fuzzy matching + embeddings + LLM judge для неоднозначных кластеров.
7. Добавить нормализацию units из `packs/alloys-v1.yaml` (MPa ↔ ksi и т.п.).

## Done
- `GET /health` возвращает `{ "status": "ok" }`.
- `POST /extract` без LLM возвращает валидный `ExtractResponse` из mock.
- Все `edge.source_claims` ссылаются на существующие `claim.id`.
- Все `edge.src` / `edge.dst` ссылаются на существующие `entity.id`.
- Невалидный input даёт `{ "error": { "code": ..., "message": ..., "details": ... } }`.
- Агент не содержит логики `rank`, `score_total`, `status`.

