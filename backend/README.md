# Backend

API-сервер, Telegram-бот и интеграции (Tezo, ЮKassa, S3).

**ТЗ:** см. `docs/TZ_Backend.md`  
**Схема БД:** `docs/DB_Schema.md`

---

## Логика директорий

- **api/** — FastAPI: роуты, сервисы, модели (Mini App API, Admin API).
- **bot/** — aiogram: обработчики команд и сообщений, интеграция с API.
- **integrations/** — клиенты Tezo, ЮKassa, S3.
- **core/** — конфиг, БД, логирование.
- **alembic/** — миграции (при использовании Alembic).

Точная структура уточняется при старте разработки по ТЗ.
