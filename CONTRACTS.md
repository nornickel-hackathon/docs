# CONTRACTS.md — JSON-контракты MVP

## Общие правила
- JSON использует `snake_case`.
- Все id стабильные строковые: `claim_001`, `node_strength`, `hyp_001`.
- Frontend не придумывает данные, а отображает `BoardResponse`.
- Python Agent отдаёт extraction, но не ставит финальный ranking/status.
- Rust API валидирует вход от Agent.

## KpiContract
```json
{
  "target": {
    "metric": "strength",
    "direction": "increase",
    "minimum_delta_percent": 10
  },
  "constraints": [
    { "metric": "cost", "op": "<=", "value": 5, "unit": "percent" }
  ],
  "weights_override": {
    "cost": 0.2
  },
  "excluded_factors": ["node_sc_addition"]
}
```

`weights_override` и `excluded_factors` опциональны и используются для rerun.

## ExtractResponse
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

## Claim
```json
{
  "id": "claim_001",
  "text": "Artificial aging changes precipitate state and affects hardness.",
  "source_ref": "doc_aging_2618a",
  "confidence": 0.86,
  "evidence_type": "literature"
}
```

## GraphNode
```json
{
  "id": "node_aging_time",
  "kind": "factor",
  "label": "aging time",
  "tags": ["controllable"],
  "properties": {
    "unit": "h"
  }
}
```

## GraphEdge
```json
{
  "id": "edge_001",
  "src": "node_aging_time",
  "dst": "node_precipitate_state",
  "edge_type": "mechanism",
  "mechanism": "precipitate_evolution",
  "source_claims": ["claim_001"],
  "polarity": "positive"
}
```

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

`kpi_contract` — контракт, по которому был запущен этот run (нужен фронту для отображения активных ограничений и rerun-формы).

## Hypothesis
```json
{
  "id": "hyp_001",
  "title": "Tune aging time to improve strength with controlled ductility loss",
  "summary": "Change aging window to improve precipitate strengthening.",
  "status": "recommended",
  "rank": 1,
  "score_total": 0.82,
  "score_breakdown": {
    "kpi_impact": 0.85,
    "evidence": 0.88,
    "plausibility": 0.84,
    "cost": 0.9,
    "risk": 0.65,
    "novelty": 0.45
  },
  "trace": ["claim_001", "claim_002"],
  "source_nodes": ["node_aging_time", "node_precipitate_state", "node_strength"],
  "risks": ["ductility impact needs direct measurement"],
  "missing_evidence": ["no claim covers fatigue behavior"],
  "doe_plan": {
    "objective": "Verify strength and ductility after adjusted aging.",
    "factors": ["aging temperature", "aging time"],
    "measurements": ["yield strength", "hardness", "elongation", "cost delta"],
    "minimum_runs": 6
  }
}
```

## Статусы гипотез
- `recommended`: можно показывать как top-кандидат.
- `watch`: перспективно, но есть существенный риск или пробел evidence.
- `rejected_by_constraints`: нарушает hard constraint.
- `needs_expert_review`: не хватает evidence по важному ограничению.

## RerunAction
```json
{
  "kind": "change_weight",
  "payload": {
    "dimension": "cost",
    "value": 0.3
  }
}
```

Поддерживаемые `kind` для MVP:
- `exclude_factor`;
- `change_weight`;
- `add_constraint`;
- `relax_constraint`.

