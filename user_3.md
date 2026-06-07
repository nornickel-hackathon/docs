# user_3.md — Prompt для Frontend

Ты работаешь в проекте “Фабрика гипотез”. Твоя роль: **Роль 3 — Frontend**.

Твоя зона ответственности:
- React/TypeScript UI;
- portfolio board;
- hypothesis card;
- trace/source view;
- score breakdown;
- risks/missing evidence;
- DOE plan;
- rerun controls.

## Что прочитать сначала
Прочитай эти файлы из папки `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `web/docs/TYPESCRIPT.md`
6. `web/docs/UI_GUIDELINES.md`
7. `frontend_tasks.md`

## Главная цель
Сделать P0 demo UI, который работает от `fixtures/board.json` без backend.

Пользователь должен за 2 секунды понять:
- какая гипотеза top-1;
- почему она предложена;
- на каких источниках основана;
- какие риски;
- как её проверить.

## Делай только P0
Не делай пока:
- сложный manager dashboard;
- Tauri;
- auth;
- charts ради charts;
- backend-логику;
- LLM integration.

## P0 задачи
1. Создай React/TypeScript приложение.
2. Включи strict TypeScript.
3. Не используй `any`.
4. Сетевой слой держи в `src/api.ts`.
5. Опиши frontend-типы по `CONTRACTS.md` или подключи сгенерированные типы, когда Rust Track их даст.
6. Сделай loading `BoardResponse` из `fixtures/board.json`.
7. Реализуй:
   - KPI input;
   - portfolio board;
   - hypothesis card;
   - trace/source view;
   - score breakdown;
   - risks/missing evidence;
   - DOE plan;
   - rerun controls.

## Данные для разработки
Используй:
- `fixtures/board.json`;
- `fixtures/extract_response.json`.

Эти файлы лежат внутри `docs/`.

## UI требования
- Статусы должны быть видны:
  - `recommended`;
  - `watch`;
  - `rejected_by_constraints`;
  - `needs_expert_review`.
- Trace должен показывать claim text и source.
- Score breakdown должен быть понятен без чтения документации.
- DOE plan должен быть отдельным видимым блоком.
- Rerun controls могут сначала работать как mock.

## Done
Работа готова, когда:
- UI запускается;
- board показывает 4 гипотезы из fixture;
- можно открыть карточку гипотезы;
- карточка показывает trace, score, risks, missing evidence, DOE;
- rerun action меняет board через mock или API adapter;
- нет `any`;
- текст не расползается и интерфейс пригоден для демо.

