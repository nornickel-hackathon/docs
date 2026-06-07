# API_CONVENTIONS.md

## Принципы
- Единственный источник структур — `crates/contracts` (serde). TS-типы генерятся (`ts-rs`).
- Граница Python↔Rust — только JSON. Rust валидирует на входе. Без PyO3.
- snake_case в JSON. Версия контракта в заголовке `X-Contract-Version`.
- Ошибка: `{ "error": { "code": str, "message": str, "details": any } }`, HTTP 4xx/5xx.

## Python-сайдкар (порт 8765) — «ничего не решает»
- `POST /extract`  body: {docs:[{path,mime}], pack_id} → {claims:[Claim], entities, edges}
- `POST /embed`    body: {texts:[str]} → {vectors:[[f32]]}
- `POST /skeptic`  body: {hypothesis} → {objection, missing_evidence, risks}
- `POST /narrate`  body: {hypothesis} → {text}

## Платформа axum (порт 8080)
- `POST /ingest`          body: {folder, pack_id} → стартует фоновую задачу → {job_id}
- `GET  /ingest/:job_id`  → {status: "pending"|"running"|"done"|"error", progress?: int, snapshot_id?: str, error?: str}
- `POST /run`             body: {snapshot_id, kpi_contract, pack_id} → {board: BoardResponse}
- `POST /rerun`           body: {run_id, action} → пересчёт нужного этапа → {board}
- `GET  /board?run_id=`   → BoardResponse
- `GET  /hypothesis/:id`  → Hypothesis (trace, score, skeptic, doe)

### Rerun action (enum)
```json
{
  "kind": "exclude_factor" | "relax_constraint" | "add_constraint" | "change_weight",
  "payload": {
    "factor_id": "...",          // для exclude_factor
    "constraint_kpi": "cost",    // для relax/add_constraint
    "op": "<=", "value": 10,     // для relax/add_constraint
    "dimension": "kpi_impact",   // для change_weight
    "value": 0.35                // для change_weight
  }
}
```

## Pack loading — кто и когда читает
- **Роль 1 (Rust Track/platform)** читает `packs/<pack_id>.yaml` при старте `POST /run` → десериализует в `DomainPack` struct → передаёт в `engine::discover()`.
- **Роль 2 (sidecar)** читает тот же файл самостоятельно при `POST /extract` (получает `pack_id` в body) → использует только для нормализации единиц и выбора промптов. Pack не кешируется между запросами на этапе хакатона.

## Ключевые типы (контракт)
KpiContract{target, constraints[]} · Claim{id,text,source_ref,confidence} ·
GraphNode{id,kind,tags[]} · GraphEdge{src,dst,edge_type,mechanism,source_ref} ·
ScoreBreakdown{kpi_impact,evidence,plausibility,cost,risk,novelty,...} ·
Hypothesis{id,status,trace[],score_breakdown,doe_plan} · BoardResponse{snapshot,hash,hypotheses[]}
