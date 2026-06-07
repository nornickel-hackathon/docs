# frontend_tasks.md — Роль 3: Frontend

## Цель роли
Собрать демо-интерфейс, который показывает ценность продукта: портфель гипотез,
trace к источникам, score breakdown, риски и DOE-план.

Frontend должен работать от `fixtures/board.json` с первого дня.

## Входы
- `CONTRACTS.md` — структура данных.
- `fixtures/board.json` — основной mock.
- `fixtures/extract_response.json` — claims/entities для trace details.
- `DEMO_SCENARIO.md` — demo flow.

## P0 задачи
1. Создать React/TypeScript приложение.
2. Включить строгий TypeScript:
   - `strict: true`;
   - без `any`;
   - сетевой слой только в `src/api.ts`.
3. Описать frontend-типы по `CONTRACTS.md` или импортировать сгенерированные типы, когда Rust Track их отдаст.
4. Реализовать API adapter:
   - сначала читает `fixtures/board.json`;
   - после интеграции читает `GET /board`.
5. Реализовать экраны:
   - KPI input;
   - portfolio board;
   - hypothesis card;
   - trace/source view;
   - score breakdown;
   - risks/missing evidence;
   - DOE plan;
   - rerun controls.

## UX приоритеты
- За 2 секунды должно быть видно, какая гипотеза top-1 и почему.
- Trace должен показывать claims и источники, а не просто красивый текст.
- Статусы должны быть различимы:
  - `recommended`;
  - `watch`;
  - `rejected_by_constraints`;
  - `needs_expert_review`.
- Rerun controls должны быть простыми:
  - исключить factor;
  - повысить вес cost;
  - добавить/ослабить constraint.

## P1 задачи
1. Подключить реальный backend API.
2. Добавить loading/error states.
3. Добавить comparison before/after rerun.
4. Добавить source panel, где видно claim text и document title.
5. Добавить export demo summary.

## Done
- UI открывается и показывает 4 гипотезы из `fixtures/board.json`.
- Каждая карточка отвечает на:
  - почему эта гипотеза;
  - на каких claims основана;
  - какие риски;
  - как проверить экспериментом.
- Rerun action меняет board через mock или API.

