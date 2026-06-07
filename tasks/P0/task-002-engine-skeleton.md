# P0 · task-002 · Скелет Discovery Engine  (Роль 1)
**Цель:** прогнать захардкоженный мини-граф до одной гипотезы с trace.
**Сделать:**
- petgraph-граф из примера docs/WORKED_EXAMPLE.md (Sc/aging) — как тестовая фикстура в коде.
- Оператор `mechanism_path` (generic: incoming по edge_type до tag controllable).
- Минимальный scoring (взвешенная сумма из pack-чисел) + один хард-фильтр.
- Сборка Hypothesis{trace, score_breakdown}.
**Done:** unit-тест и golden-тест зелёные; в коде нет доменных слов.
