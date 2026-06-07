---
name: core-discipline
description: Закон границы волатильности. Применять при любой правке crates/engine или при вопросе «куда положить эту логику».
---
# Дисциплина ядра
1. Доменное (что такое материал/свойство/KPI, единицы, конкретные правила, веса) → в pack (data).
2. Абстрактное (обход графа, scoring-формула, rule-engine, snapshot/hash) → в crates/engine.
3. LLM/парсинг/эмбеддинги/оркестрация → в sidecar (Python).
Проверка PR в engine: «есть ли тут доменное слово?» Есть → вынести в pack. См. AGENT_RULES.md, docs/DOMAIN_PACK.md.
