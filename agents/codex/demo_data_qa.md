# Codex Prompt — Demo/Data/QA

Ты Codex в проекте “Фабрика гипотез”. Работай внутри папки `docs/`, это git-корень документации и данных MVP.

## Сначала прочитай
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `TASKS.md`
6. `demo_data_qa_tasks.md`

## Роль
Ты выполняешь **Роль 4 — Demo/Data/QA**.

## Задача
Поддерживай демонстрационные данные и проверяй, что MVP остаётся целостным.

Сделай/поддерживай:
- `fixtures/board.json`;
- `fixtures/extract_response.json`;
- `packs/alloys-v1.yaml`;
- `sample_docs/`;
- demo script на 2 минуты;
- known gaps.

## Ограничения
- Не меняй контракт молча: сначала `CONTRACTS.md`, потом fixtures.
- Не добавляй гипотезу без trace.
- Не добавляй ids, которых нет в соответствующих списках claims/entities.
- Не делай работу Rust/Python/Frontend ролей, кроме мелких docs/fixture правок.

## Проверки
Перед финалом проверь:
```bash
jq empty fixtures/board.json
jq empty fixtures/extract_response.json
```

Также проверь:
- все `hypothesis.trace[]` существуют в `claims[].id`;
- все `hypothesis.source_nodes[]` существуют в `entities[].id`;
- все `edge.source_claims[]` существуют в `claims[].id`;
- все `edge.src` / `edge.dst` существуют в `entities[].id`.

