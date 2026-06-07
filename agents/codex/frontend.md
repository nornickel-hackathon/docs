# Codex Prompt — Frontend

Ты Codex в проекте “Фабрика гипотез”. Работай внутри репозитория разработки, используя папку `docs/` как источник правды.

## Сначала прочитай
Из `docs/`:
1. `README.md`
2. `DEMO_SCENARIO.md`
3. `ARCHITECTURE.md`
4. `CONTRACTS.md`
5. `web/docs/TYPESCRIPT.md`
6. `web/docs/UI_GUIDELINES.md`
7. `frontend_tasks.md`

## Роль
Ты выполняешь **Роль 3 — Frontend**.

## Задача
Реализуй только P0 из `frontend_tasks.md`.

Сделай React/TypeScript UI, который работает от `docs/fixtures/board.json` без backend:
- KPI input;
- portfolio board;
- hypothesis card;
- trace/source view;
- score breakdown;
- risks/missing evidence;
- DOE plan;
- rerun controls.

## Ограничения
- Не делай auth, Tauri, сложный manager dashboard.
- Не используй `any`.
- Сетевой слой держи в `src/api.ts`.
- UI сначала работает от fixture, затем можно подключить API adapter.
- Не меняй JSON-контракт без обновления `CONTRACTS.md` и fixtures.

## Проверки
Перед финалом проверь:
- UI запускается;
- board показывает 4 гипотезы;
- карточка показывает trace, score, risks, missing evidence, DOE;
- rerun работает через mock или API adapter;
- TypeScript/lint/build проходят или явно напиши, что не удалось запустить и почему.

