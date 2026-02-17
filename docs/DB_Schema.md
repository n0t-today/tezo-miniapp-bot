# Схема базы данных

Отдельный документ с описанием структуры БД.  
Связанные документы: `TZ.md`, `TZ_Backend.md` (в этой же папке docs/).  
СУБД: PostgreSQL.

---

## 1. Общая информация

- **Формат дат в БД:** TIMESTAMP с timezone; при отдаче в API — строка в формате dd.mm.yyyy (HH:mm при необходимости).
- **Идентификаторы:** UUID или BIGSERIAL (на усмотрение команды). В схеме ниже — `id BIGSERIAL PRIMARY KEY` для краткости.
- **Мягкое удаление:** при необходимости — поле `deleted_at`; в ТЗ по умолчанию — физическое удаление.

---

## 2. Таблицы

### 2.1 users — пользователи (Telegram)

Пользователи мини-приложения, идентифицируемые по Telegram.

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| telegram_user_id | BIGINT | UNIQUE. ID пользователя в Telegram |
| telegram_username | VARCHAR(255) | @username (nullable) |
| telegram_first_name | VARCHAR(255) | Имя (nullable) |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Индексы:** `telegram_user_id` UNIQUE.

---

### 2.2 tezo_bindings — привязка Tezo

Связь пользователя с аккаунтом Tezo (карта или телефон).

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| user_id | BIGINT | FK → users.id |
| tezo_type | VARCHAR(20) | 'card' \| 'phone' |
| tezo_value | VARCHAR(100) | Номер карты или телефона (зашифровано/хеш при необходимости) |
| tezo_account_id | VARCHAR(255) | ID аккаунта в системе Tezo (nullable, если API не возвращает) |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Ограничения:** один активный binding на пользователя (UNIQUE user_id при логике «одна привязка»). При отвязке — удаление или `unbound_at`.

**Индексы:** `user_id`, `tezo_value` (для поиска дублей).

---

### 2.3 products — товары магазина

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| name | VARCHAR(500) | Название |
| price_bonuses | DECIMAL(12,2) | Цена в бонусах |
| price_rubles | DECIMAL(12,2) | Цена в рублях |
| discount_percent | DECIMAL(5,2) | Скидка в % (0 = без скидки) |
| in_raffle | BOOLEAN | Участвует в розыгрыше |
| stock | INTEGER | Остаток (0 = нет в наличии) |
| has_sizes | BOOLEAN | Есть размеры |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

---

### 2.4 product_sizes — размеры товаров

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| product_id | BIGINT | FK → products.id ON DELETE CASCADE |
| size_value | VARCHAR(50) | Значение размера (S, M, L, 42 и т.д.) |
| sort_order | INTEGER | Порядок отображения |

**Индексы:** `product_id`.

---

### 2.5 product_images — изображения товаров (S3)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| product_id | BIGINT | FK → products.id ON DELETE CASCADE |
| url | VARCHAR(1000) | URL изображения в S3 |
| s3_key | VARCHAR(500) | Ключ в S3 (для удаления) |
| is_main | BOOLEAN | Основное фото |
| sort_order | INTEGER | Порядок в галерее |

**Индексы:** `product_id`.

---

### 2.6 raffles — розыгрыши

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| spins_until_win | INTEGER | Лимит спинов до гарантированного выигрыша |
| spins_remaining | INTEGER | Оставшееся количество спинов (глобальный счётчик) |
| status | VARCHAR(20) | 'active' \| 'completed' |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Логика:** только один активный розыгрыш (`status = 'active'`). При `spins_remaining = 0` следующий спин — гарантированный выигрыш; после него `status = 'completed'`.

---

### 2.7 raffle_prizes — призы розыгрыша

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| raffle_id | BIGINT | FK → raffles.id ON DELETE CASCADE |
| product_id | BIGINT | FK → products.id (nullable). Ссылка на товар для списания остатка и отображения |
| name | VARCHAR(500) | Название приза (если product_id null или переопределение) |
| image_url | VARCHAR(1000) | URL изображения (S3). Если product_id задан — можно использовать фото товара |
| price_bonuses | DECIMAL(12,2) | Цена в бонусах (для отображения) |
| probability_percent | DECIMAL(5,2) | Вероятность выпадения в % |
| has_sizes | BOOLEAN | Есть размеры |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Примечание:** при `product_id` не null — размеры берутся из `product_sizes`. Иначе — отдельная таблица `raffle_prize_sizes` (аналогично `product_sizes`).

---

### 2.8 raffle_prize_sizes — размеры призов (если приз не связан с товаром)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| raffle_prize_id | BIGINT | FK → raffle_prizes.id ON DELETE CASCADE |
| size_value | VARCHAR(50) | |
| sort_order | INTEGER | |

---

### 2.9 promo_codes — промокоды для выигранных товаров

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| code | VARCHAR(50) | UNIQUE. Код промокода |
| user_id | BIGINT | FK → users.id |
| raffle_prize_id | BIGINT | FK → raffle_prizes.id |
| product_id | BIGINT | FK → products.id (nullable) |
| size_value | VARCHAR(50) | Выбранный размер (nullable) |
| used_at | TIMESTAMPTZ | Когда применён (nullable) |
| order_id | BIGINT | FK → orders.id (nullable, при использовании) |
| created_at | TIMESTAMPTZ | |

**Индексы:** `code` UNIQUE, `user_id`.

---

### 2.10 cart_items — позиции корзины

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| user_id | BIGINT | FK → users.id ON DELETE CASCADE |
| product_id | BIGINT | FK → products.id |
| raffle_prize_id | BIGINT | FK → raffle_prizes.id (nullable, если позиция — выигрыш) |
| size_value | VARCHAR(50) | Выбранный размер |
| quantity | INTEGER | Количество |
| price_bonuses | DECIMAL(12,2) | Цена в бонусах за единицу (0 для выигрыша) |
| price_rubles | DECIMAL(12,2) | Цена в рублях за единицу (0 для выигрыша) |
| promo_code_id | BIGINT | FK → promo_codes.id (nullable, для выигранной позиции) |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Уникальность:** одна позиция = один товар + один размер + один promo_code (если выигрыш). Уникальный индекс: `(user_id, product_id, size_value, promo_code_id)` или логика в приложении.

**Индексы:** `user_id`.

---

### 2.11 orders — заказы

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| user_id | BIGINT | FK → users.id |
| status | VARCHAR(30) | 'new', 'paid', 'processing', 'completed', 'cancelled' |
| total_bonuses | DECIMAL(12,2) | Сумма бонусами |
| total_rubles | DECIMAL(12,2) | Сумма рублями |
| address | TEXT | Адрес доставки |
| delivery_type | VARCHAR(20) | 'courier' \| 'pickup' |
| comment | TEXT | Комментарий (nullable) |
| telegram_topic_id | INTEGER | ID темы в супергруппе (nullable) |
| yookassa_payment_id | VARCHAR(255) | ID платежа ЮKassa (nullable) |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Индексы:** `user_id`, `status`, `created_at`.

---

### 2.12 order_items — позиции заказа

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| order_id | BIGINT | FK → orders.id ON DELETE CASCADE |
| product_id | BIGINT | FK → products.id |
| raffle_prize_id | BIGINT | FK → raffle_prizes.id (nullable) |
| size_value | VARCHAR(50) | |
| quantity | INTEGER | |
| price_bonuses | DECIMAL(12,2) | |
| price_rubles | DECIMAL(12,2) | |
| promo_code_id | BIGINT | FK → promo_codes.id (nullable) |

**Индексы:** `order_id`.

---

### 2.13 appeal_categories — категории обращений (справочник)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| name | VARCHAR(100) | «Вопрос по заказу», «Жалоба», «Сотрудничество», «Другое» |
| sort_order | INTEGER | |

---

### 2.14 appeals — обращения (тикеты)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| user_id | BIGINT | FK → users.id |
| category_id | BIGINT | FK → appeal_categories.id |
| status | VARCHAR(30) | 'open', 'answered', 'closed' |
| telegram_topic_id | INTEGER | ID темы в супергруппе (nullable) |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Индексы:** `user_id`, `status`, `created_at`.

---

### 2.15 appeal_messages — сообщения в обращении

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| appeal_id | BIGINT | FK → appeals.id ON DELETE CASCADE |
| sender_type | VARCHAR(20) | 'user' \| 'manager' |
| sender_id | BIGINT | user_id или admin_user_id (nullable, в зависимости от sender_type) |
| text | TEXT | |
| telegram_message_id | BIGINT | ID сообщения в ТГ (nullable) |
| created_at | TIMESTAMPTZ | |

**Индексы:** `appeal_id`.

---

### 2.16 admins — админы бота (Telegram)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| telegram_id | BIGINT | UNIQUE. Telegram ID админа |
| created_at | TIMESTAMPTZ | |

**Индексы:** `telegram_id` UNIQUE.  
**Сид:** при первой миграции вставить `7533811917`.

---

### 2.17 roles — роли веб-админки

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| name | VARCHAR(100) | «Суперадмин», «Менеджер заказов» и т.д. |
| permissions | JSONB | `{ "raffle": ["read","write"], "shop": ["read","write"], "orders": ["read"], "appeals": ["read","write"], "users": ["read","write"] }` |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

---

### 2.18 admin_users — пользователи веб-админки

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| login | VARCHAR(100) | UNIQUE |
| password_hash | VARCHAR(255) | Хеш пароля (bcrypt) |
| telegram_id | BIGINT | Для входа через Telegram (nullable) |
| role_id | BIGINT | FK → roles.id |
| created_at | TIMESTAMPTZ | |
| updated_at | TIMESTAMPTZ | |

**Индексы:** `login` UNIQUE, `telegram_id`, `role_id`.

---

### 2.19 raffle_spins_log — лог спинов (опционально, для аналитики)

| Поле | Тип | Описание |
|------|-----|----------|
| id | BIGSERIAL | PK |
| raffle_id | BIGINT | FK → raffles.id |
| user_id | BIGINT | FK → users.id |
| won | BOOLEAN | Выигрыш или нет |
| prize_id | BIGINT | FK → raffle_prizes.id (nullable) |
| created_at | TIMESTAMPTZ | |

**Индексы:** `raffle_id`, `user_id`, `created_at`.

---

## 3. Диаграмма связей (кратко)

```
users ──┬── tezo_bindings
        ├── cart_items ── products, raffle_prizes, promo_codes
        ├── orders ── order_items ── products, raffle_prizes, promo_codes
        ├── appeals ── appeal_messages
        └── promo_codes

products ── product_sizes, product_images, cart_items, order_items

raffles ── raffle_prizes ── raffle_prize_sizes (опционально)
raffle_prizes ── products (nullable)

admins (отдельно, для бота)
admin_users ── roles (веб-админка)
```

---

## 4. Миграции

- Инструмент: **Alembic** (или аналог).
- Порядок создания таблиц: users, tezo_bindings, products, product_sizes, product_images, raffles, raffle_prizes, raffle_prize_sizes, promo_codes, cart_items, orders, order_items, appeal_categories, appeals, appeal_messages, admins, roles, admin_users, raffle_spins_log.
- Сидовые данные: `appeal_categories` (категории), `admins` (7533811917), `roles` (суперадмин), первый `admin_user` и `raffle` при первом запуске.

---

**Перейти к следующему документу:** `TZ_Backend.md` — ТЗ бэкенда (API, интеграции).
