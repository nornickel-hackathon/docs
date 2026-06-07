# WORKED_EXAMPLE.md — Sc + aging через generic-операторы

Цель — показать команде, что «абстрактный обход» это не лозунг, а конкретный код,
который не знает ни слова «сплав».

## Вход (всё — данные, не код)
KPI-контракт: `strength target +10%`, ограничение `cost <= +5%`, `ductility drop <= 3%`.
Claims (из сайдкара):
- C1 [paper2018]: «Sc 0.1–0.3% измельчает зерно в Al-сплавах»
  → edge `Sc --mechanism(grain_refinement)--> microstructure`, Sc.tags=[controllable]
- C2 [handbook]: «измельчение зерна повышает прочность (Hall-Petch)»
  → edge `microstructure --mechanism(hall_petch)--> strength`, strength.tags=[kpi]
- C3 [report2019]: «Sc дорогой, +6% к стоимости»
  → cost-аннотация на рычаге Sc

## Что делает engine (generic)
1. Старт у узла с tag `kpi` = `strength`.
2. Оператор `mechanism_path`: идём по **incoming** рёбрам типа `mechanism`, пока не упрёмся
   в узел с tag `controllable`. Путь: `strength ← hall_petch ← microstructure ← grain_refinement ← Sc`.
   → кандидат «поднять Sc 0.1–0.3%». **Оператор не знает слов Sc / Hall-Petch** — только
   `edge_type=mechanism` и `tag=controllable`.
3. Scoring (веса из pack): evidence=2 грунтованных claim'а, plausibility=2 известных механизма,
   cost из C3 = +6%.
4. Хард-фильтр `cost <= +5%` → **нарушен** → если строго, статус `Rejected by constraints`;
   либо ищем substitution-оператором более дешёвый рычаг с тем же путём к strength.
5. Skeptic-rule `uncovered_constraint`: в контракте есть `ductility`, но ни один claim его не
   покрывает → флаг «Need expert review: DOE обязан измерить пластичность».
6. Сборка `Hypothesis{ trace:[C1,C2,C3], score_breakdown, status, doe_plan }`.

## Мораль
Sc, Hall-Petch, MPa, порог cost — всё в claims и pack (данные).
В `crates/engine` лежит только `follow_incoming(edge_type, until: tag)`, scoring-формула и rule-engine.
Сменили домен → меняются claims и pack, обход тот же.
