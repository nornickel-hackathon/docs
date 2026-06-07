# Фабрика гипотез — скелет проекта

Старт для команды:
1. **DEMO_SCENARIO.md** — что именно показываем на хакатоне.
2. **CONTRACTS.md** — JSON-контракты между Rust, Python и frontend.
3. **ARCHITECTURE.md** — как устроены Rust Track, Python Agent, Frontend и Demo/Data/QA.
4. **TASKS.md** — независимая раздача работ на 4 человека.
5. Role-specific задачи: **rust_tasks.md**, **agent_tasks.md**, **frontend_tasks.md**,
   **demo_data_qa_tasks.md**.
6. Готовые prompt-файлы для агентов: **user_1.md**, **user_2.md**, **user_3.md**,
   **user_4.md**.

## Быстрый старт для участников

- Rust-разработчик получает **user_1.md**.
- Python/Agent-разработчик получает **user_2.md**.
- Frontend-разработчик получает **user_3.md**.
- Demo/Data/QA получает **user_4.md**.

Если заранее известно, каким агентом пользуется участник, лучше дать agent-specific prompt:
- Codex: `agents/codex/rust.md`, `agents/codex/agent.md`, `agents/codex/frontend.md`,
  `agents/codex/demo_data_qa.md`.
- Claude Code: `agents/claude/rust.md`, `agents/claude/agent.md`,
  `agents/claude/frontend.md`, `agents/claude/demo_data_qa.md`.

Дополнительный контекст:
- **AGENTS.md** — источник инженерных правил.
- **ROLES.md** — роль каждого участника.
- **PROJECT_CONTEXT.md** + **docs/boundary.svg** — расширенная архитектура и граница волатильности.
- **DEFINITION_OF_DONE.md** — критерии готовности.
- **AGENT_RULES.md** — жёсткие запреты.

Ключевое для тезиса проекта: **docs/DOMAIN_PACK.md**, **docs/WORKED_EXAMPLE.md**,
`fixtures/board.json`, `fixtures/extract_response.json`, `packs/alloys-v1.yaml`.
