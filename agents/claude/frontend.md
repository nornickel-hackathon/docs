# Claude Code Prompt — Frontend

Ты Claude Code в проекте “Фабрика гипотез”. Работай по документации из `docs/`.

## Контекст перед работой
Сначала изучи:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `web/docs/TYPESCRIPT.md`
6. `web/docs/UI_GUIDELINES.md`
7. `frontend_tasks.md`

После чтения кратко сформулируй план P0 и приступай к реализации.

## Роль
Ты отвечаешь за **Роль 3 — Frontend**.

## Что нужно сделать
Выполни P0 из `frontend_tasks.md`.

UI должен работать от `docs/fixtures/board.json` без backend и показывать:
- KPI input;
- portfolio board;
- hypothesis card;
- trace/source view;
- score breakdown;
- risks/missing evidence;
- DOE plan;
- rerun controls.

## Не распыляйся
Не делай в P0:
- auth;
- Tauri;
- сложный manager dashboard;
- backend implementation;
- LLM integration.

## Критические правила
- TypeScript strict.
- Не используй `any`.
- Сетевой слой только в `src/api.ts`.
- Данные сначала из fixture.
- Если нужно поменять контракт, сначала объясни зачем, затем обнови `CONTRACTS.md` и fixtures вместе.

## Финальная проверка
В конце покажи:
- что реализовано;
- как запустить UI;
- какие команды запускались;
- прошли ли lint/build/tests;
- какие ограничения остались.

