# prompt_frontend.md — Prompt для Frontend

Ты работаешь в проекте "Фабрика гипотез". Твоя роль: **Роль 3 — Frontend**.

Твоя зона ответственности: `web/` — React/TypeScript демо-интерфейс.

## Что прочитать сначала
1. `DEMO_SCENARIO.md` — что показываем и 4 вопроса, на которые должна отвечать карточка
2. `CONTRACTS.md` — все типы данных и enum-значения (статусы, edge_type, evidence_type и т.д.)
3. `web/docs/UI_GUIDELINES.md` — UX-принципы и layout
4. `web/docs/TYPESCRIPT.md` — правила TypeScript
5. `frontend_tasks.md` — задачи P0/P1, стек, структура файлов, примеры кода

## Главная цель
P0 demo UI, работающий от `fixtures/board.json` без backend.

Пользователь должен за 2 секунды понять:
- какая гипотеза top-1 и её статус
- почему она предложена (trace → claims → источники)
- какие риски
- как проверить экспериментом

## Делай только P0
Не делай пока:
- реальные API-запросы к backend (только mock из fixtures)
- Manager View, Budget What-If, Data Readiness (P1)
- Tauri, auth, charts ради charts
- backend-логику или Rust-код

## Стек (уже выбран)
Vite + React 18 + TypeScript + Tailwind + shadcn/ui + TanStack Query + pnpm

## Ключевые правила
- Все типы — из `src/contracts.ts` (не объявлять вручную доменные типы)
- `any` запрещён — eslint error
- `fetch` только в `src/api.ts`, компоненты не зовут fetch напрямую
- P0: `src/api.ts` читает из fixtures; переключение на реальный API через `VITE_API_URL`

## Данные для разработки
- `docs/fixtures/board.json` — 4 гипотезы с разными статусами
- `docs/fixtures/extract_response.json` — claims и entities для trace panel

## Done
- `pnpm dev` запускается без ошибок
- Board показывает 4 гипотезы с цветными статусами
- Карточка отвечает на 4 вопроса (почему / источники / риски / эксперимент)
- Trace показывает claim.text + название документа, не просто id
- Нет `any`, нет fetch в компонентах
- Rerun action меняет порядок на доске
