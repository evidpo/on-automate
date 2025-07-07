# Calls Analytics

## Описание

Calls Analytics — система для хранения и анализа клиентских взаимодействий: звонков, лидов, контактов, компаний и продуктов. Позволяет отслеживать полный путь клиента, фиксировать детали звонков, встречи, email и автоматически строить историю взаимодействий.

## Структура данных

### 1. Компании (COMPANIES)
- id: уникальный идентификатор
- name: название
- inn, ogrn, okved: реквизиты
- company_type: тип (наша, клиентская и др.)
- industry, size: отрасль, размер
- purchase_count, total_revenue: покупки и выручка
- company_profile_embedding: вектор профиля для поиска похожих
- extras: дополнительные атрибуты
- created_at, updated_at: даты

### 2. Контакты (CONTACTS)
- id: уникальный идентификатор
- company_id: ссылка на компанию
- name, role, phone, email: данные контакта
- is_decision_maker, is_our_employee: ЛПР, сотрудник/клиент
- extras: соцсети, предпочтения
- created_at, updated_at: даты

### 3. Лиды и клиентский путь (LEADS)
- id: уникальный идентификатор
- contact_id: ссылка на контакт
- source, category, qualification, urgency_level: параметры лида
- crm_status, current_stage, stage_goals, goals_achieved
- journey_history: история этапов
- interaction_timeline: вся история взаимодействий (звонки, email, встречи, демо)
- extras: дополнительные атрибуты
- created_at, updated_at: даты

#### Пример interaction_timeline:
- timeline: массив событий (call, email, meeting, demo)
- summary: агрегированные метрики (количество, даты, длительность)

### 4. Звонки (CALLS)
- id: уникальный идентификатор
- lead_id, our_company_id: ссылки
- direction, communication_type: направление, тип
- started_at, ended_at, duration_seconds, day_of_week, hour_of_day, timezone
- outcome, success_reason, failure_reason
- talk_time_ratio, interruptions_count, emotional_tone
- next_step_agreement, agreement_strength
- full_transcript, transcript_embedding, summary
- participants_info, phrases_breakdown, behavioral_metrics, topics_and_tags, products_discussed, post_call_events, geo_and_context
- recording_url, previous_call_id, extras
- created_at, updated_at: даты

### 5. Продукты (PRODUCTS)
- id: уникальный идентификатор
- name, price, currency, category, training_direction, description
- document_url, description_embedding
- call_discussions, extras
- created_at, updated_at: даты

## Связи между таблицами
- Компании ← Контакты (company_id)
- Контакты ← Лиды (contact_id)
- Лиды ← Звонки (lead_id)
- Звонки → Звонки (previous_call_id)
- Продукты связаны со звонками через поле products_discussed

## Особенности истории взаимодействий
- При добавлении звонка: создаётся запись в CALLS и автоматически добавляется в LEADS.interaction_timeline
- При добавлении email/встречи/демо: добавляется только в LEADS.interaction_timeline, обновляется LEADS.summary с метриками