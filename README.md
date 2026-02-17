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
│   ├── README_TZ.md
│   ├── TZ_Backend.md
│   ├── TZ_Frontend.md
│   ├── TZ_Mini_App.md
│   ├── TZ_Web_Admin.md
│   └── DB_Schema.md
└── README.md        # этот файл
```

---

## Документация

Вся техническая документация — в папке **docs/**.

- **docs/README_TZ.md** — оглавление комплекта ТЗ.
- **docs/TZ.md** — общее ТЗ проекта.
- **docs/DB_Schema.md** — схема базы данных PostgreSQL.

### Порядок изучения ТЗ

1. **docs/TZ.md** — общее понимание проекта.
2. **docs/DB_Schema.md** — структура данных.
3. **docs/TZ_Backend.md** — серверная часть и API.
4. **docs/TZ_Frontend.md** — клиентская часть.
5. **docs/TZ_Mini_App.md** и **docs/TZ_Web_Admin.md** — детали экранов и страниц.

В конце каждого документа указан переход к следующему.

---

## Компоненты

| Компонент | Путь | Назначение |
|-----------|------|------------|
| Backend | `backend/` | REST API, Telegram-бот, Tezo, ЮKassa, S3 |
| Mini App | `frontend/miniapp/` | Розыгрыш, магазин, корзина для пользователей |
| Web Admin | `frontend/admin/` | Управление товарами, розыгрышами, заказами, обращениями |

Дата: 17.02.2025. Язык: русский.
