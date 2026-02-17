# Проект: Telegram-бот с мини-приложением (Tezo, розыгрыш, магазин)

Общая директория проекта. Структура по логикам: бэкенд, фронтенд (мини-приложение и веб-админка), документация.

---

## Структура проекта

```
tezo-miniapp-bot/
├── backend/          # API, Telegram-бот, интеграции (Tezo, ЮKassa, S3)
├── frontend/
│   ├── miniapp/     # Telegram Mini App (React)
│   └── admin/       # Веб-админка (React)
├── docs/            # ТЗ и схема БД
│   ├── TZ.md
│   ├── TZ_Mini_App.md
│   ├── TZ_Web_Admin.md
│   ├── TZ_Backend.md
│   ├── TZ_Frontend.md
│   ├── DB_Schema.md
│   └── README_TZ.md
└── README.md        # этот файл
```

---

## Документация

Вся техническая документация — в папке **docs/**.

- **docs/README_TZ.md** — оглавление комплекта ТЗ и порядок изучения.
- **docs/TZ.md** — общее ТЗ проекта.
- **docs/DB_Schema.md** — схема базы данных PostgreSQL.

---

## Компоненты

| Компонент | Путь | Назначение |
|-----------|------|------------|
| Backend | `backend/` | REST API, Telegram-бот, Tezo, ЮKassa, S3 |
| Mini App | `frontend/miniapp/` | Розыгрыш, магазин, корзина для пользователей |
| Web Admin | `frontend/admin/` | Управление товарами, розыгрышами, заказами, обращениями |

Дата: 17.02.2025. Язык: русский.
