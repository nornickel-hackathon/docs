# frontend_tasks.md — Роль 3: Frontend

## Цель роли
Собрать демо-интерфейс, который показывает ценность продукта: портфель гипотез,
trace к источникам, score breakdown, риски и DOE-план.

Frontend работает от `fixtures/board.json` с первого дня — не ждёт backend.

## Стек
- **Vite** + React 18 + TypeScript (`strict: true`, `noUncheckedIndexedAccess: true`)
- **Tailwind CSS** — стилизация
- **shadcn/ui** — готовые компоненты (Card, Badge, Tabs, Slider, Button)
- **TanStack Query** — серверный стейт и query cache
- **pnpm** — пакетный менеджер

## Входы
- `CONTRACTS.md` — структура всех типов
- `web/src/contracts.ts` — сгенерированные TS-типы (от Rust Track; до получения — вручную из `CONTRACTS.md`)
- `fixtures/board.json` — основной mock
- `fixtures/extract_response.json` — claims/entities для trace details
- `DEMO_SCENARIO.md` — demo flow и 4 вопроса, на которые должна отвечать карточка
- `web/docs/UI_GUIDELINES.md` — UX-принципы

## Структура проекта

```
web/src/
  contracts.ts        ← сгенерирован из crates/contracts (не редактировать вручную)
  api.ts              ← единственное место с fetch; остальные компоненты не зовут fetch
  mock.ts             ← loadBoard() и loadHypothesis() из fixtures (для P0)
  App.tsx
  pages/
    KpiInputPage.tsx
    BoardPage.tsx
    HypothesisPage.tsx
  components/
    HypothesisCard.tsx
    ScoreBreakdown.tsx
    TracePanel.tsx
    DoePlan.tsx
    RerunPanel.tsx
    StatusBadge.tsx
```

## src/api.ts — интерфейс (не менять сигнатуры без обновления CONTRACTS.md)

```typescript
export async function getBoard(runId?: string): Promise<BoardResponse>
export async function getHypothesis(id: string): Promise<Hypothesis>
export async function postRerun(runId: string, action: RerunAction): Promise<BoardResponse>
export async function postRun(snapshotId: string, kpi: KpiContract): Promise<BoardResponse>
```

P0: функции читают из `fixtures/board.json`. P1: переключаются на `GET /board` (axum, порт 8080).
Переключение через `VITE_API_URL` env variable: если не задан — mock, если задан — реальный API.

## P0 задачи

### 1. Scaffold
```bash
pnpm create vite web --template react-ts
cd web && pnpm add tailwindcss @shadcn/ui @tanstack/react-query
```
Настроить `tsconfig.json`: `strict: true`, `noUncheckedIndexedAccess: true`.

### 2. Типы
Временно скопировать типы из `CONTRACTS.md` в `src/contracts.ts`.
Когда Rust Track отдаст сгенерированный файл — заменить импорт, не менять компоненты.

### 3. Mock API (`src/mock.ts`)
```typescript
import board from '../../docs/fixtures/board.json'
export const getBoard = async (): Promise<BoardResponse> => board as BoardResponse
```

### 4. StatusBadge
Цвета статусов (видны сразу на доске):
- `recommended` → зелёный
- `watch` → жёлтый
- `rejected_by_constraints` → красный
- `needs_expert_review` → оранжевый

### 5. BoardPage — портфель гипотез
- Список карточек, отсортированный по `rank`
- Каждая карточка: title, status badge, score_total, топ-3 риска одной строкой
- Клик → HypothesisPage

### 6. HypothesisPage — карточка гипотезы
Вкладки (Tabs):

**Вкладка "Почему"**
- `summary`
- Trace: список `trace[]` → для каждого claim_id показать `claim.text` и `claim.source_ref`
  (данные из `fixtures/extract_response.json`, загрузить отдельно)

**Вкладка "Оценка"**
- ScoreBreakdown: горизонтальные бары для каждого из 6 измерений с подписями
- `score_total` крупно

**Вкладка "Риски"**
- `risks[]` — список
- `missing_evidence[]` — список с иконкой предупреждения

**Вкладка "Эксперимент"**
- DoePlan: objective, factors, measurements, minimum_runs

### 7. RerunPanel
Форма с тремя действиями (mock, без реального API):
- Исключить factor: выбрать из `source_nodes` текущей гипотезы
- Изменить вес: слайдер для `cost` (0.05 → 0.50)
- Ослабить constraint: поле для нового value

После submit → показать mock-ответ (изменить порядок гипотез локально для демо).

### 8. KpiInputPage
Форма:
- `target.metric` (строка)
- `target.direction` (increase / decrease)
- `target.minimum_delta_percent` (число)
- Кнопка добавить constraint (metric, op, value)
- Submit → перейти на BoardPage

## UX приоритеты
- За 2 секунды видно: топ-гипотеза, её статус, почему предложена
- Trace показывает claim text + источник документа, не просто id
- Score breakdown понятен без документации (подписи к каждому бару)
- Статусы различимы цветом на доске
- Rerun controls простые: одно действие → один клик

## P1 задачи
1. Переключить `src/api.ts` на реальный backend (axum порт 8080)
2. Loading и error states через TanStack Query
3. Diff-вид до/после rerun: какие гипотезы поднялись/упали
4. Source panel: полный текст claim + название документа
5. Manager View: только бюджет, отдача, статусы — без слова "claim"
6. Budget What-If: слайдер весов → портфель пересобирается

## Done
- `pnpm dev` запускается без ошибок
- Board показывает 4 гипотезы из fixture с правильными цветами статусов
- Карточка отвечает на 4 вопроса: почему / источники / риски / как проверить
- Trace показывает текст claim и название документа (не просто id)
- Score breakdown читаем без документации
- Нет `any`, нет прямых `fetch` в компонентах
- Rerun action меняет порядок на доске
