# P0 · task-004 · /extract в сайдкаре  (Роль 2)
**Цель:** из документов получить claims по схеме.
**Сделать:** FastAPI `POST /extract`; парсер PDF/DOCX/CSV; LLM-извлечение под pydantic (LLM можно замокать фиксированным ответом); нормализация единиц по pack.
**Done:** на 2 тестовых PDF возвращается валидный {claims, entities, edges}.
