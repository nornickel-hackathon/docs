# P0 · task-001 · Зафиксировать контракт  (Роль 1, при участии всех)
**Цель:** единый источник структур + TS-типы, чтобы 4 человека пошли параллельно.
**Сделать:**
- В `crates/contracts` объявить: KpiContract, Claim, GraphNode, GraphEdge, ScoreBreakdown, Hypothesis, BoardResponse, DoePlan.
- Подключить ts-rs, сгенерить `web/src/contracts.ts`.
- Положить `fixtures/board.json` (3–5 гипотез) по этим типам.
**Done:** типы компилируются в Rust и TS; фикстура валидна; импортируется во фронте.
