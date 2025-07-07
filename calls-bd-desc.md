# 1. COMPANIES — Компании (наши и клиентские)

| Поле                         | Тип               | Описание                                                         |
|------------------------------|-------------------|------------------------------------------------------------------|
| `id`                         | `BIGINT`          | Уникальный ID компании                                           |
| `name`                       | `VARCHAR(255)`    | Название компании                                                |
| `inn`                        | `VARCHAR(12)`     | ИНН компании                                                     |
| `ogrn`                       | `VARCHAR(15)`     | ОГРН компании                                                    |
| `okved`                      | `VARCHAR(10)`     | Код ОКВЭД                                                        |
| `company_type`               | `VARCHAR(50)`     | Тип: "наша", "клиентская", "государственная", "частная", "ИП"     |
| `industry`                   | `VARCHAR(100)`    | Отрасль деятельности                                             |
| `size`                       | `VARCHAR(50)`     | Размер: "малый", "средний", "крупный"                            |
| `purchase_count`             | `INTEGER`         | Количество покупок у нас                                         |
| `total_revenue`              | `DECIMAL(15,2)`   | Общая выручка с компании                                         |
| `company_profile_embedding`  | `VECTOR(1536)`    | 🎯 Вектор профиля компании для поиска похожих                     |
| `extras`                     | `JSONB`           | Дополнительные атрибуты (регион, сайт, описание и т.д.)          |
| `created_at`                 | `TIMESTAMPTZ`     | Когда добавлена                                                 |
| `updated_at`                 | `TIMESTAMPTZ`     | Когда обновлена                                                 |

---

# 2. CONTACTS — Контакты людей

| Поле                 | Тип              | Описание                                                   |
|----------------------|------------------|------------------------------------------------------------|
| `id`                 | `BIGINT`         | Уникальный ID контакта                                     |
| `company_id`         | `BIGINT`         | Ссылка на компанию                                         |
| `name`               | `VARCHAR(255)`   | Имя контакта                                               |
| `role`               | `VARCHAR(100)`   | Роль/должность                                            |
| `phone`              | `VARCHAR(50)`    | Телефон                                                   |
| `email`              | `VARCHAR(255)`   | Email                                                     |
| `is_decision_maker`  | `BOOLEAN`        | ЛПР или не ЛПР                                            |
| `is_our_employee`    | `BOOLEAN`        | Наш сотрудник или клиент                                   |
| `extras`             | `JSONB`          | Дополнительные данные (соцсети, предпочтения и т.д.)      |
| `created_at`         | `TIMESTAMPTZ`    | Когда добавлен                                            |
| `updated_at`         | `TIMESTAMPTZ`    | Когда обновлен                                           |

---

# 3. LEADS — Лиды и клиентский путь с полной историей 🎯

| Поле                 | Тип              | Описание                                                                 |
|----------------------|------------------|--------------------------------------------------------------------------|
| `id`                 | `BIGINT`         | Уникальный ID лида                                                       |
| `contact_id`         | `BIGINT`         | Ссылка на контакт                                                        |
| `source`             | `VARCHAR(100)`   | Источник лида (реклама, реферал, холодный и т.д.)                        |
| `category`           | `VARCHAR(100)`   | Категория лида                                                           |
| `qualification`      | `VARCHAR(50)`    | "квалифицированный", "неквалифицированный"                               |
| `urgency_level`      | `INTEGER`        | Срочность потребности (1–10)                                             |
| `crm_status`         | `VARCHAR(100)`   | Текущий статус в CRM                                                     |
| `current_stage`      | `VARCHAR(100)`   | Текущий этап клиентского пути                                            |
| `stage_goals`        | `TEXT`           | Цели текущего этапа                                                      |
| `goals_achieved`     | `BOOLEAN`        | Достигнуты ли цели этапа                                                 |
| `journey_history`    | `JSONB`          | История этапов воронки и переходов                                       |
| `interaction_timeline` | `JSONB`        | 🎯 Полная история всех взаимодействий                                    |
| `extras`             | `JSONB`          | Дополнительные атрибуты лида                                              |
| `created_at`         | `TIMESTAMPTZ`    | Когда создан лид                                                         |
| `updated_at`         | `TIMESTAMPTZ`    | Когда обновлен                                                           |


Структура interaction_timeline (JSONB):


{
  "timeline": [
    {
      "id": "call_123",
      "type": "call",
      "date": "2025-01-15T10:00:00Z",
      "outcome": "success", 
      "call_id": 123,
      "duration": 1800,
      "participants": ["manager_1", "client_1"],
      "next_step": "demo",
      "notes": "Клиент заинтересован в продукте А"
    },
    {
      "id": "email_456", 
      "type": "email",
      "date": "2025-01-16T14:30:00Z",
      "subject": "Презентация продукта А",
      "direction": "outgoing",
      "opened": true,
      "clicked_links": 2
    },
    {
      "id": "meeting_789",
      "type": "meeting", 
      "date": "2025-01-20T11:00:00Z",
      "outcome": "contract_signed",
      "duration": 3600,
      "location": "офис клиента",
      "participants": ["manager_1", "client_1", "client_boss"]
    },
    {
      "id": "demo_321",
      "type": "demo",
      "date": "2025-01-18T15:00:00Z", 
      "product": "Product A",
      "outcome": "interested",
      "questions_count": 5
    }
  ],
  "summary": {
    "total_interactions": 4,
    "first_contact": "2025-01-15T10:00:00Z",
    "last_contact": "2025-01-20T11:00:00Z",
    "days_in_pipeline": 5,
    "calls_count": 1,
    "emails_count": 1, 
    "meetings_count": 1,
    "demos_count": 1,
    "total_duration_minutes": 150
  }
}

# 4. CALLS — Звонки (основная таблица)

| Поле                    | Тип               | Описание                                                                                 |
|-------------------------|-------------------|------------------------------------------------------------------------------------------|
| `id`                    | `BIGINT`          | Уникальный ID звонка                                                                     |
| `lead_id`               | `BIGINT`          | Ссылка на лид                                                                            |
| `our_company_id`        | `BIGINT`          | Какая наша компания вела звонок                                                         |
| **📞 ОСНОВНАЯ ИНФОРМАЦИЯ** |                   |                                                                                          |
| `direction`             | `VARCHAR(20)`     | `"входящий"`, `"исходящий"`                                                             |
| `communication_type`    | `VARCHAR(50)`     | `"холодный"`, `"теплый"`, `"повторный"`                                                 |
| `started_at`            | `TIMESTAMPTZ`     | Время начала звонка                                                                      |
| `ended_at`              | `TIMESTAMPTZ`     | Время окончания звонка                                                                   |
| `duration_seconds`      | `INTEGER`         | Длительность в секундах                                                                   |
| `day_of_week`           | `INTEGER`         | День недели (1–7)                                                                        |
| `hour_of_day`           | `INTEGER`         | Час дня (0–23)                                                                           |
| `timezone`              | `VARCHAR(50)`     | Часовой пояс                                                                             |
| **🎯 РЕЗУЛЬТАТ**           |                   |                                                                                          |
| `outcome`               | `VARCHAR(50)`     | `"успех"`, `"отказ"`, `"перенос"`, `"недозвон"`                                          |
| `success_reason`        | `TEXT`            | Причина успеха                                                                           |
| `failure_reason`        | `TEXT`            | Причина отказа                                                                           |
| **📊 ПОВЕДЕНЧЕСКИЕ МЕТРИКИ** |                   |                                                                                          |
| `talk_time_ratio`       | `FLOAT`           | TTR (соотношение времени говорения менеджер/клиент)                                       |
| `interruptions_count`   | `INTEGER`         | Количество перебиваний                                                                   |
| `emotional_tone`        | `VARCHAR(50)`     | `"anger"`, `"joy"`, `"neutral"`, `"frustrated"`                                          |
| **🤝 ДОГОВОРЕННОСТИ**       |                   |                                                                                          |
| `next_step_agreement`   | `TEXT`            | Договоренность на следующий шаг                                                           |
| `agreement_strength`    | `INTEGER`         | Сила договоренности (1–10)                                                                |
| **💬 КОНТЕНТ ЗВОНКА**       |                   |                                                                                          |
| `full_transcript`       | `TEXT`            | Полная транскрипция звонка                                                               |
| `transcript_embedding`  | `VECTOR(1536)`    | 🎯 Вектор всего текста для семантического поиска                                          |
| `summary`               | `TEXT`            | Краткое саммари звонка                                                                   |
| **👥 УЧАСТНИКИ И ДЕТАЛИ**    |                   |                                                                                          |
| `participants_info`     | `JSONB`           | Все участники, их роли, метрики речи, время говорения                                    |
| `phrases_breakdown`     | `JSONB`           | Детализация по фразам с временными метками (если нужно)                                  |
| `behavioral_metrics`    | `JSONB`           | Скорость речи, паузы, изменения тона по участникам                                       |
| `topics_and_tags`       | `JSONB`           | Авто-теги тем (pricing, complaint), ключевые слова                                       |
| `products_discussed`    | `JSONB`           | Какие продукты обсуждались, уровень интереса                                            |
discussed_product_ids
| `post_call_events`      | `JSONB`           | События после звонка (demo_booked, invoice_sent)                                         |
| `geo_and_context`       | `JSONB`           | Геометка, погода, контекст дня                                                           |
| **🔗 ТЕХНИЧЕСКИЕ**         |                   |                                                                                          |
| `recording_url`         | `TEXT`            | Ссылка на запись звонка                                                                  |
| `previous_call_id`      | `BIGINT`          | Ссылка на предыдущий звонок с этим лидом                                                 |
| `extras`                | `JSONB`           | Любые дополнительные данные                                                              |
| `created_at`            | `TIMESTAMPTZ`     | Когда создан                                                                             |
| `updated_at`            | `TIMESTAMPTZ`     | Когда обновлен                                                                           |

---

# 5. PRODUCTS — Продукты и предложения

| Поле                    | Тип               | Описание                                                    |
|-------------------------|-------------------|-------------------------------------------------------------|
| `id`                    | `BIGINT`          | Уникальный ID продукта                                      |
| `name`                  | `VARCHAR(255)`    | Название продукта                                           |
| `price`                 | `DECIMAL(15,2)`   | Цена                                                        |
| `currency`              | `VARCHAR(10)`     | Валюта (RUB, USD)                                           |
| `category`              | `VARCHAR(100)`    | Категория продукта                                          |
| `training_direction`    | `VARCHAR(100)`    | Направление обучения                                        |
| `description`           | `TEXT`            | Описание продукта                                           |
| `document_url`          | `TEXT`            | Ссылка на документ/презентацию                              |
| `description_embedding` | `VECTOR(1536)`    | 🎯 Вектор описания для рекомендаций                         |
| `call_discussions`      | `JSONB`           | В каких звонках обсуждался, результаты                      |
| `extras`                | `JSONB`           | Дополнительные атрибуты (фичи, преимущества)                |
| `created_at`            | `TIMESTAMPTZ`     | Когда добавлен                                             |
| `updated_at`            | `TIMESTAMPTZ`     | Когда обновлен                                             |

---

## 🔗 Связи между таблицами

- `COMPANIES` ← `CONTACTS` (по `company_id`)  
- `CONTACTS` ← `LEADS` (по `contact_id`)  
- `LEADS` ← `CALLS` (по `lead_id`)  
- `CALLS` → `CALLS` (по `previous_call_id`)  
- `PRODUCTS` связаны с `CALLS` через JSONB поле (`products_discussed`)

---

## 🎯 Как работает история взаимодействий

1. **При добавлении звонка**  
   - Создается запись в `CALLS`  
   - Автоматически добавляется в `LEADS.interaction_timeline`

2. **При добавлении email/встречи/демо**  
   - Добавляется только в `LEADS.interaction_timeline`  
   - Обновляется `LEADS.summary` с метриками 