# crates/engine — Discovery Engine (Роль 1)
ЯДРО. Чистый Rust, БЕЗ I/O, БЕЗ HTTP, БЕЗ доменных слов (AGENT_RULES.md §1).
- Вход: граф + KpiContract + pack-параметры (числа/правила). Выход: ранжированный портфель.
- Операторы — generic, над `EdgeType` и тегами узлов. Никаких `Sc`/`alloy`/`strength`.
- Детерминизм обязателен; случайность только с seed. Каждый PR обновляет golden-тест.
- Зависимости: petgraph. НЕ зависит от platform/sidecar.
