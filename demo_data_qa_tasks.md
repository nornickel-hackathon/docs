# demo_data_qa_tasks.md — Роль 4: Demo/Data/QA

## Цель роли
Сделать так, чтобы команда писала не абстрактную платформу, а демонстрируемый продукт:
с валидными fixtures, понятным corpus, проверяемым demo flow и коротким pitch.

## Входы
- `task.txt` — исходная задача.
- `DEMO_SCENARIO.md` — основной сценарий.
- `CONTRACTS.md` — контракт данных.
- `fixtures/`;
- `packs/`;
- `sample_docs/`.

## P0 задачи
1. Поддерживать `fixtures/board.json`.
2. Поддерживать `fixtures/extract_response.json`.
3. Поддерживать `packs/alloys-v1.yaml`.
4. Поддерживать `sample_docs/`.
5. Проверять ссылочную целостность:
   - все `hypothesis.trace[]` существуют в `claims[].id`;
   - все `hypothesis.source_nodes[]` существуют в `entities[].id`;
   - все `edge.source_claims[]` существуют в `claims[].id`;
   - все `edge.src` / `edge.dst` существуют в `entities[].id`.
6. Готовить 2-минутный demo script:
   - проблема;
   - input KPI;
   - extraction;
   - ranked portfolio;
   - top hypothesis trace;
   - expert rerun.

## P1 задачи
1. Добавить ещё 1-2 sample documents.
2. Подготовить backup mode: демо работает даже без LLM и без интернета.
3. Собрать known gaps:
   - PDF parsing пока простой;
   - LLM extraction optional;
   - Materials Project только future extension;
   - SQLite/Tauri/FAISS после MVP.
4. Подготовить acceptance checklist перед показом.

## Быстрые проверки
```bash
jq empty fixtures/board.json
jq empty fixtures/extract_response.json
jq '.kpi_contract != null' fixtures/board.json
jq '.hypotheses[0].source_nodes | length > 0' fixtures/board.json
```

## Done
- Все fixtures валидны.
- Demo flow проходится за 2 минуты.
- Каждая top-гипотеза имеет trace, score, risks и DOE.
- Команда понимает, что является MVP, а что является future work.

