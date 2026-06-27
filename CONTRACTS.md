# CONTRACTS.md — единый источник контракта

Это главный справочник. Все остальные файлы ссылаются сюда, но не дублируют.

## Общие правила
- JSON: `snake_case`, id — стабильные строки (`claim_001`, `node_strength`, `hyp_001`).
- Frontend только отображает `BoardResponse` — не придумывает данные.
- Python Agent только извлекает — не ставит `rank`, `score_total`, `status`.
- Rust API валидирует весь JSON от Python перед использованием.
- Изменение любой структуры → обновить этот файл + регенерировать `web/src/contracts.ts` в том же PR.

## Порты
| Сервис | Порт |
|--------|------|
| Python sidecar | 8765 |
| Rust platform (axum) | 8080 |
| Frontend (Vite dev) | 5173 |

---

## ExtractRequest
> Rust Platform → Python Sidecar при `POST /extract`

```json
{
  "docs": [
    { "path": "sample_docs/aging_2618a.txt", "mime": "text/plain" },
    { "path": "sample_docs/internal_experiments.csv", "mime": "text/csv" }
  ],
  "pack_id": "alloys-v1"
}
```

Rust сканирует папку пользователя, собирает пути и mime-типы.

| Поле | Тип | Описание |
|------|-----|----------|
| `docs[].path` | string | путь к файлу (относительный от корня проекта) |
| `docs[].mime` | enum | `"text/plain"` \| `"text/csv"` \| `"application/pdf"` (PDF — P1) |
| `pack_id` | string | id domain pack, используется для нормализации |

---

## ExtractResponse
> Python Sidecar → Rust Platform

```json
{
  "pack_id": "alloys-v1",
  "documents": [
    {
      "id": "doc_aging_2618a",
      "title": "Long-term aging of S-phase in aluminum alloy 2618A",
      "path": "sample_docs/aging_2618a.txt",
      "source_url": "https://link.springer.com/article/10.1007/s10853-020-05740-x"
    }
  ],
  "claims": [],
  "entities": [],
  "edges": []
}
```

`source_url` — опциональный, `null` для внутренних документов.

---

## Claim

```json
{
  "id": "claim_001",
  "text": "Artificial aging changes precipitate state and affects hardness.",
  "source_ref": "doc_aging_2618a",
  "confidence": 0.88,
  "evidence_type": "literature"
}
```

| `evidence_type` | Когда использовать |
|----------------|-------------------|
| `"literature"` | факт из научной статьи или справочника |
| `"experiment"` | факт из CSV/таблицы с экспериментальными данными |
| `"expert_note"` | комментарий эксперта в тексте документа |
| `"data_gap"` | явное указание на отсутствие данных |
| `"inferred"` | LLM вывел по косвенным признакам (ставить низкий confidence) |

---

## GraphNode

```json
{
  "id": "node_aging_time",
  "kind": "factor",
  "label": "aging time",
  "tags": ["controllable"],
  "properties": { "unit": "h", "estimated_cost_delta_percent": 0.5 }
}
```

| `kind` | Смысл |
|--------|-------|
| `"factor"` | управляемый параметр процесса |
| `"mechanism"` | промежуточное физическое явление |
| `"property"` | измеримое свойство материала |
| `"kpi"` | целевой или ограничивающий показатель |

| `tags[]` | Смысл |
|----------|-------|
| `"controllable"` | фактор, которым можно управлять в эксперименте |
| `"kpi"` | целевой KPI (engine идёт от него назад) |
| `"constraint"` | KPI, который нельзя нарушать (cost, ductility_loss) |
| `"kpi_proxy"` | свойство, которое косвенно отражает KPI (hardness ≈ strength) |

`properties` — свободный объект; `unit` и `estimated_cost_delta_percent` — рекомендуемые поля.

---

## GraphEdge

```json
{
  "id": "edge_001",
  "src": "node_aging_time",
  "dst": "node_precipitate_state",
  "edge_type": "mechanism",
  "mechanism": "precipitate_evolution",
  "source_claims": ["claim_001", "claim_003"],
  "polarity": "positive"
}
```

| `edge_type` | Смысл |
|-------------|-------|
| `"mechanism"` | причинная связь: фактор/механизм влияет на другой узел |
| `"proxy"` | косвенная связь (hardness → strength) |
| `"tradeoff"` | отрицательный побочный эффект (Sc → cost) |
| `"substitution"` | альтернативный путь с тем же эффектом (Zr ↔ Sc) |

| `polarity` | Смысл |
|-----------|-------|
| `"positive"` | увеличение src → увеличение dst |
| `"negative"` | увеличение src → уменьшение dst |
| `"nonlinear"` | нелинейная зависимость (оптимум существует) |

`mechanism` — свободная строка-метка (живёт в данных, не в engine-коде).

---

## KpiContract

```json
{
  "target": {
    "metric": "strength",
    "direction": "increase",
    "minimum_delta_percent": 10
  },
  "constraints": [
    { "metric": "cost", "op": "<=", "value": 5, "unit": "percent" },
    { "metric": "ductility_loss", "op": "<=", "value": 3, "unit": "percent" }
  ],
  "weights_override": { "cost": 0.3 },
  "excluded_factors": ["node_sc_addition"]
}
```

`weights_override` и `excluded_factors` — опциональны, только для rerun. `op`: `"<="` | `">="`.

---

## ScoreBreakdown

```json
{
  "kpi_impact": 0.86,
  "evidence": 0.88,
  "plausibility": 0.84,
  "cost": 0.92,
  "risk": 0.66,
  "novelty": 0.45
}
```

Все поля `[0.0, 1.0]`. Формулы расчёта — в `docs/SCORING.md`. Веса — в `DomainPack.scoring_weights`.

---

## DoePlan

```json
{
  "objective": "Verify strength and ductility after adjusted aging.",
  "factors": ["aging temperature", "aging time"],
  "measurements": ["yield strength", "hardness", "elongation", "cost delta"],
  "minimum_runs": 6
}
```

---

## Hypothesis

```json
{
  "id": "hyp_001",
  "title": "Tune aging window to improve strength",
  "summary": "Change aging time/temp to improve precipitate strengthening.",
  "status": "recommended",
  "rank": 1,
  "score_total": 0.82,
  "score_breakdown": { },
  "trace": ["claim_001", "claim_002", "claim_003"],
  "source_nodes": ["node_aging_time", "node_precipitate_state", "node_strength"],
  "risks": ["ductility impact needs direct measurement"],
  "missing_evidence": ["no claim covers fatigue behavior"],
  "doe_plan": { }
}
```

| `status` | Условие назначения (код, не LLM) |
|----------|----------------------------------|
| `"recommended"` | `score_total >= 0.75` и нет нарушений hard constraint |
| `"watch"` | `score_total >= 0.55` и нет нарушений |
| `"rejected_by_constraints"` | нарушен любой `hard_constraint` из pack |
| `"needs_expert_review"` | constraint-метрика не покрыта ни одним claim |

---

## BoardResponse

```json
{
  "snapshot": {
    "id": "snapshot_demo_001",
    "hash": "demo-hash-001",
    "pack_id": "alloys-v1"
  },
  "kpi_contract": { },
  "hypotheses": []
}
```

`kpi_contract` нужен фронту для отображения активных ограничений и формы rerun.  
`snapshot.hash` не меняется при rerun — меняется только ranking внутри `hypotheses[]`.

---

## DomainPack
> Rust internal — десериализуется из `packs/<pack_id>.yaml`, передаётся в `engine::discover()`

```json
{
  "pack_id": "alloys-v1",
  "scoring_weights": {
    "kpi_impact":   0.30,
    "evidence":     0.25,
    "plausibility": 0.15,
    "cost":         0.15,
    "risk":         0.10,
    "novelty":      0.05
  },
  "hard_constraints": [
    { "metric": "cost",           "op": "<=", "value": 5.0 },
    { "metric": "ductility_loss", "op": "<=", "value": 3.0 }
  ],
  "enabled_operators": ["mechanism_path", "substitution", "gap"],
  "skeptic_rules": [
    { "rule": "uncovered_constraint",       "threshold": 0.0 },
    { "rule": "low_evidence_top_candidate", "threshold": 0.55 }
  ]
}
```

`KpiContract.weights_override` перекрывает отдельные ключи `scoring_weights` на время rerun без изменения pack-файла.

---

## Graph (Rust internal)

```rust
pub type Graph = petgraph::DiGraph<GraphNode, GraphEdge>;
```

Platform строит граф из `ExtractResponse`, передаёт `&Graph` в `engine::discover()`.  
Engine граф не строит и не хранит.

---

## RerunAction

```json
{ "kind": "exclude_factor",   "payload": { "factor_id": "node_sc_addition" } }
{ "kind": "change_weight",    "payload": { "dimension": "cost", "value": 0.3 } }
{ "kind": "add_constraint",   "payload": { "metric": "cost", "op": "<=", "value": 3 } }
{ "kind": "relax_constraint", "payload": { "metric": "cost", "op": "<=", "value": 10 } }
```

---

## Формат ошибок (оба сервиса)

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "edge src references unknown entity",
    "details": { "edge_id": "edge_009", "missing_node": "node_xyz" }
  }
}
```

| HTTP | Когда |
|------|-------|
| 400 | некорректный запрос (неверная структура JSON) |
| 422 | валидный JSON, но не прошёл бизнес-валидацию |
| 500 | внутренняя ошибка сервиса |
