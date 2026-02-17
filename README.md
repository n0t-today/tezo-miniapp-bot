# Проект: Telegram-бот с мини-приложением (Tezo, розыгрыш, магазин)

Общая директория проекта. Структура по логикам: бэкенд, фронтенд (мини-приложение и веб-админка), документация.

---

## Структура проекта

```
tezo-miniapp-bot/
├── backend/              # API, Telegram-бот, интеграции (Tezo, ЮKassa, S3)
│   ├── docs/             # ТЗ бэкенда и схема БД
│   │   ├── TZ_Backend.md
│   │   └── DB_Schema.md
│   └── README.md
├── frontend/
│   ├── docs/             # Общее ТЗ фронтенда
│   │   └── TZ_Frontend.md
│   ├── miniapp/          # Telegram Mini App (React)
│   │   ├── docs/
│   │   │   └── TZ_Mini_App.md
│   │   └── README.md
│   └── admin/            # Веб-админка (React)
│       ├── docs/
│       │   └── TZ_Web_Admin.md
│       └── README.md
├── docs/                 # Общее ТЗ проекта
│   ├── TZ.md             # Общее ТЗ
│   └── README_TZ.md      # Оглавление комплекта ТЗ
└── README.md             # этот файл
```

---

## Документация

ТЗ распределены по компонентам проекта:

- **docs/README_TZ.md** — оглавление комплекта ТЗ и порядок изучения.
- **docs/TZ.md** — общее ТЗ проекта.
- **backend/docs/** — ТЗ бэкенда и схема БД PostgreSQL.
- **frontend/docs/** — общее ТЗ фронтенда.
- **frontend/miniapp/docs/** — ТЗ мини-приложения.
- **frontend/admin/docs/** — ТЗ веб-админки.

---

## Компоненты

| Компонент | Путь | Назначение |
|-----------|------|------------|
| Backend | `backend/` | REST API, Telegram-бот, Tezo, ЮKassa, S3 |
| Mini App | `frontend/miniapp/` | Розыгрыш, магазин, корзина для пользователей |
| Web Admin | `frontend/admin/` | Управление товарами, розыгрышами, заказами, обращениями |

Дата: 17.02.2025. Язык: русский.
