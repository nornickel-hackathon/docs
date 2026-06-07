# DOMAIN_PACK.md — как домен живёт в данных, а не в коде

Pack — это **вся доменная семантика**, вынесенная из ядра в один файл (`packs/<name>.yaml`).
Меняешь pack → меняешь домен. Ядро не пересобирается. Это и есть «честная универсальность».

## Схема pack
```yaml
id: alloys-v1
units:                      # нормализация единиц
  strength: MPa
  temperature: C
node_types:                 # какие бывают узлы графа
  - factor                  # управляемый рычаг (tag: controllable)
  - mechanism
  - property
  - kpi                     # цель (tag: kpi)
edge_types:                 # типы рёбер, по которым ходят generic-операторы
  - mechanism               # факт «A влияет на B через механизм»
  - tradeoff                # «A улучшает X, но ухудшает Y»
  - substitution            # «A заменяем на B»
operators:                  # какие generic-операторы включены для домена
  - mechanism_path
  - substitution
  - process_window
scoring_weights:            # читает Scoring-движок в engine
  kpi_impact: 0.30
  evidence: 0.25
  plausibility: 0.15
  cost: 0.15
  risk: 0.10
  novelty: 0.05
hard_constraints:           # нарушение → status=Rejected by constraints (фильтр, не штраф)
  - { kpi: cost, op: "<=", value: 5, unit: "%" }
skeptic_rules:              # читает Skeptic-rules engine в engine
  - id: uncovered_constraint
    when: "kpi_contract has constraint with no claim on that property"
    flag: "Need expert review: <property> impact not evidenced"
```

## Как ядро это читает (generic)
- `operators` → engine включает только перечисленные обходы. Сам оператор не знает «сплав» —
  он знает «иди по incoming-рёбрам типа `mechanism`, пока не дойдёшь до узла с tag `controllable`».
- `scoring_weights` / `hard_constraints` → подставляются в формулу scoring как числа.
- `skeptic_rules` → интерпретируются rule-engine'ом над структурой графа и KpiContract.
- `units` → нормализатор (в сайдкаре) приводит извлечённые величины к канону.

## Дополнительные теги (`extra_tags`)

Pack может объявлять теги сверх стандартных `controllable` / `kpi`:

- **`constraint`** — на `kpi`-узлы, которые являются ограничением, а не целью. Engine не ищет путь к ним как к KPI, но проверяет hard_constraints по их метрике.
- **`kpi_proxy`** — на `property`-узлы, косвенно отражающие KPI через задокументированный механизм (например, `hardness` → `strength`). Включаются в trace как промежуточный сигнал.

Тег, не объявленный в `extra_tags` — engine игнорирует молча. Новый тег = правка pack, не правка engine.

## Кто и когда присваивает теги `controllable` и `kpi`

Теги присваиваются **сайдкаром** при `/extract` на основе `node_types` из pack:
- Узел получает тег `controllable`, если его `kind` совпадает с `node_types.factor` в pack.
- Узел получает тег `kpi`, если его `kind` совпадает с `node_types.kpi` в pack.

Engine видит только теги (`tags: Vec<String>`) и не знает откуда они взялись.
Platform валидирует при приёме JSON: каждый граф обязан содержать хотя бы один `kpi`-узел.

## Пивот = новый pack + новый сайдкар
Сменили домен (катализаторы / repurposing лекарств) → пишете `packs/catalysts-v1.yaml`
и адаптируете промпты извлечения в сайдкаре. `crates/engine` не меняется ни строкой.
Если пришлось менять engine — доменное слово протекло в код, см. AGENT_RULES.md §1.
