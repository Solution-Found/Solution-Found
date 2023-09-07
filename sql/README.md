# Примеры SQL запросов для PostgreSQL

## 1 пример:
Таблица **product_model**.
Подсчёт количества продуктов в каждой категории и вывод **id** только тех категорий, в которых количество продуктов больше *5*. Результат отсортирован в порядке возрастания количества продуктов:

```sql
SELECT "categoryId",
       COUNT(*) products_count
FROM product_model
GROUP BY "categoryId"
HAVING COUNT(*) > 5
ORDER BY products_count;
```

---

## 2 пример:
Таблица **product_model**.
В заказы можно вносить правки, если:
- стоимость доставки (**deliveryPrice**) больше *500*,
- стоит статус "заказ формируется" (**status** = *0*) или "заказ в доставке" (**status** = *1*)

Вывод **id** всех заказов и в колонке **update_order** возможность внесения правки к каждому заказу:

```sql
SELECT id,
       CASE
           WHEN ("deliveryPrice" > 500) AND (status = 0 OR status = 1) THEN 'yes'
           ELSE 'no'
       END update_order
FROM order_model;
```

---

## 3 пример:
Таблица **invoice**.
Подсчёт пропусков в поле с почтовым индексом **billing_postal_code** для каждой страны (**billing_country**). Полученная таблица должна содержать только те записи, в которых поле **billing_address** содержит слова **Street**, **Way**, **Road** или **Drive**, а также если у страны число пропусков больше *6*:

```sql
SELECT billing_country,
       COUNT(*)
FROM invoice
WHERE billing_postal_code IS NULL
  AND (billing_address LIKE '%Street%'
  OR billing_address LIKE '%Way%'
  OR billing_address LIKE '%Road%'
  OR billing_address LIKE '%Drive%')
GROUP BY billing_country
HAVING COUNT(*) > 6;
```

---

## 4 пример:
Таблицы **product_model** и **category_model**.
Вывод информации о продуктах, цена которых находится в диапазоне от *200* до *500*. Информация по каждому продукту включает: название продукта, цену, название категории, к которой он относится:

```sql
SELECT pm.name product_name,
       pm.price product_price,
       cm.name category_name
FROM product_model pm
LEFT JOIN category_model cm ON pm."categoryId" = cm.id
WHERE pm.price BETWEEN 200 AND 500;
```