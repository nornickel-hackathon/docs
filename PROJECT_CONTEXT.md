# PROJECT_CONTEXT.md — архитектура

## Одной фразой
Не чат с нейронкой, а R&D-платформа: из литературы и отчётов — приоритизированный,
объяснимый, проверяемый портфель экспериментов.

## Поток (pipeline)
1. **Вход:** KPI-контракт (+ ограничения) · корпус (локальная папка) · domain pack.
2. **Python-сайдкар (волатильно):** парсинг док → claims по строгой схеме →
   LLM-извлечение под валидатором → эмбеддинги/вектор-поиск → skeptic/narrator (LLM).
   Отдаёт только JSON. Ничего не решает.
3. **Rust-ядро (стабильно):** строит граф из claims → фиксирует snapshot+hash
   (граница воспроизводимости) → Discovery Engine обходит граф НАЗАД от узла KPI
   generic-операторами → Scoring + хард-фильтры (веса из pack) → Skeptic-rules →
   собирает Hypothesis с trace.
4. **Rust-платформа:** Claim-стор (sqlite), axum-API, Tauri-оболочка (file dialog,
   скан папки, спавн сайдкара, десктоп-сборка).
5. **TS-фронт:** доска-портфель (Pareto) · карточка (trace/score/skeptic/DOE) ·
   rerun-чат · Manager View / Budget What-If · Data Readiness.

## Где LLM
Только на входе (extract) и выходе (skeptic/narrate). Открытия делает детерминированный код.

## Три стора
- Структурная БД — численные факты/эксперименты (значения, единицы, условия).
- Граф механизмов — `фактор → механизм → свойство → KPI` с источниками (для trace).
- Вектор-индекс — куски документов для поиска доказательств.

## Публичный API engine (точка входа)

```rust
// crates/engine/src/lib.rs
pub fn discover(
    graph: &Graph,           // петрограф, построенный из claims platform-ом
    contract: &KpiContract,  // цель + ограничения от пользователя
    pack: &DomainPack,       // веса, операторы, правила из packs/<name>.yaml
) -> Portfolio               // ранжированный список Hypothesis с trace и score_breakdown
```

Роль 1 (Rust Track) реализует engine и platform: platform вызывает эту функцию после
построения графа из claims. Других публичных функций engine не экспортирует.

## Generic-операторы (ядро не знает домена)
mechanism-path · process-window · analogy-transfer · substitution · gap.
Каждый оператор работает над `EdgeType` и тегами узлов, не над материаловедением.
Доменная начинка — из pack. См. docs/DOMAIN_PACK.md и docs/WORKED_EXAMPLE.md.

## Граница волатильности
Схема — boundary.svg. Закон — AGENTS.md.
