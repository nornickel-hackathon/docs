---
name: api-conventions
description: Конвенции API и контракта. Применять при добавлении/изменении эндпоинтов или структур контракта.
---
# Контракт
- Структуры — только в crates/contracts; TS генерится в том же PR.
- Python↔Rust — JSON, Rust валидирует на входе, без PyO3.
- Формат ошибки и эндпоинты — docs/API_CONVENTIONS.md.
