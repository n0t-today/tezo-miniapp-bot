# Комплект ТЗ: Telegram-бот с мини-приложением (Tezo, розыгрыш, магазин)

Полноценный комплект технической документации для разработки проекта.  
Дата: 17.02.2025. Язык: русский. Формат дат: dd.mm.yyyy.

ТЗ распределены по логическим директориям компонентов проекта.

---

## Расположение документов

| Документ | Путь | Назначение |
|----------|------|------------|
| **Общее ТЗ** | `docs/TZ.md` | Цели, бот, мини-приложение, веб-админка, интеграции, логика розыгрыша |
| **ТЗ бэкенда** | `backend/docs/TZ_Backend.md` | API для Mini App и Web Admin, Telegram-бот, Tezo, ЮKassa, S3, логирование |
| **Схема БД** | `backend/docs/DB_Schema.md` | PostgreSQL: таблицы, поля, связи, индексы |
| **ТЗ фронтенда** | `frontend/docs/TZ_Frontend.md` | Стек (React, TypeScript, Vite), маршруты, компоненты для обоих приложений |
| **ТЗ Mini App** | `frontend/miniapp/docs/TZ_Mini_App.md` | Страницы мини-приложения: авторизация, розыгрыш, магазин, корзина, оформление |
| **ТЗ Web Admin** | `frontend/admin/docs/TZ_Web_Admin.md` | Страницы веб-админки: Магазин, Розыгрыш, Заказы, Обращения, Пользователи и роли |

---

## Порядок изучения

1. **docs/TZ.md** — общее понимание проекта.
2. **backend/docs/DB_Schema.md** — структура данных.
3. **backend/docs/TZ_Backend.md** — серверная часть и API.
4. **frontend/docs/TZ_Frontend.md** — клиентская часть.
5. **frontend/miniapp/docs/TZ_Mini_App.md** и **frontend/admin/docs/TZ_Web_Admin.md** — детали экранов и страниц.

---

## Ключевые технологии

| Компонент | Технология |
|-----------|------------|
| Бэкенд | Python, FastAPI, SQLAlchemy, aiogram |
| Mini App | React, TypeScript, Vite |
| Web Admin | React, TypeScript, Vite |
| БД | PostgreSQL |
| Хранилище изображений | S3 |
| Интеграции | Tezo API, ЮKassa, Telegram Bot API |
