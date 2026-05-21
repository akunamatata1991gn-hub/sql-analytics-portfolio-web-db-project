# Аналитика веб-сайта и рекламы в соцсетях с помощью SQL

В этом проекте я демонстрирую навыки работы с базой данных PostgreSQL для решения аналитических задач бизнеса (связка веб-аналитики, сайтов и маркетинга в соцсетях).

## 📊 1. Исходные данные
Для проекта создана и наполнена тестовыми данными таблица `web_analytics`. Она имитирует логи визитов, просмотры страниц, суммы покупок и источники трафика (VK, Telegram, Google).

```sql
-- Создание таблицы логов сайта
CREATE TABLE web_analytics (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(50),
    visit_date DATE,
    source VARCHAR(50),
    pages_viewed INT,
    purchase_amount NUMERIC(10, 2)
);

-- Наполнение данными (Имитация работы сайта и соцсетей)
INSERT INTO web_analytics (user_id, visit_date, source, pages_viewed, purchase_amount) VALUES
('u_101', '2026-05-01', 'VK_ads', 3, 0.00),
('u_102', '2026-05-01', 'Telegram', 12, 4500.00),
('u_101', '2026-05-02', 'VK_ads', 5, 1200.00),
('u_103', '2026-05-02', 'Google', 2, 0.00),
('u_104', '2026-05-03', 'Telegram', 8, 2300.00),
('u_102', '2026-05-03', 'Telegram', 4, 0.00),
('u_105', '2026-05-04', 'VK_ads', 1, 0.00),
('u_106', '2026-05-04', 'Google', 15, 6100.00);
```

## 💻 2. Примеры аналитических запросов

### Запрос 1. Эффективность маркетинговых каналов (Агрегация и группировка)
**Бизнес-задача:** Посчитать общую выручку, количество визитов и вовлеченность (среднее число просмотров страниц) для каждого источника трафика.
```sql
SELECT 
    source AS "Источник трафика",
    COUNT(id) AS "Всего визитов",
    SUM(purchase_amount) AS "Общая выручка",
    ROUND(AVG(pages_viewed), 1) AS "Ср. просмотров страниц"
FROM web_analytics
GROUP BY source
ORDER BY "Общая выручка" DESC;
```

### Запрос 2. Сегментация клиентов по активности (Условная логика CASE WHEN)
**Бизнес-задача:** Разделить пользователей по уровню вовлеченности на сайте для последующей настройки таргетированной рекламы.
```sql
SELECT 
    user_id,
    pages_viewed,
    CASE 
        WHEN pages_viewed >= 10 THEN 'Горячий (глубокий просмотр)'
        WHEN pages_viewed BETWEEN 3 AND 9 THEN 'Теплый'
        ELSE 'Холодный (быстрый уход)'
    END AS user_segment
FROM web_analytics;
```

### Запрос 3. Финансовая динамика (Оконные функции)
**Бизнес-задача:** Показать каждую продажу и рассчитать накопительный итог (running total) выручки по дням.
```sql
SELECT 
    visit_date,
    user_id,
    purchase_amount,
    SUM(purchase_amount) OVER (ORDER BY visit_date) AS running_total
FROM web_analytics
WHERE purchase_amount > 0;
```
