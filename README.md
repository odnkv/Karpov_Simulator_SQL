# Курс «Симулятор SQL»

## 7 урок Подзапросы

### Практика

#### [Задача 1](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257121)
Используя данные из таблицы `user_actions`, рассчитайте среднее число заказов всех пользователей нашего сервиса.

Для этого сначала в подзапросе посчитайте, сколько заказов сделал каждый пользователь, а затем обратитесь к результату подзапроса в блоке `FROM` и уже в основном запросе усредните количество заказов по всем пользователям.

Полученное среднее число заказов всех пользователей округлите до двух знаков после запятой. Колонку с этим значением назовите `orders_avg`.

Поле в результирующей таблице: `orders_avg`

```sql

SELECT round(avg(orders_count), 2) as orders_avg
FROM   (SELECT count(order_id) as orders_count,
               user_id
        FROM   user_actions
        WHERE  action = 'create_order'
        GROUP BY user_id) as subq_1

```

#### [Задача 2](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257122)
Повторите запрос из предыдущего задания, но теперь вместо подзапроса используйте оператор `WITH` и табличное выражение.

Условия задачи те же: используя данные из таблицы `user_actions`, рассчитайте среднее число заказов всех пользователей.

Полученное среднее число заказов округлите до двух знаков после запятой. Колонку с этим значением назовите `orders_avg`.

Поле в результирующей таблице: `orders_avg`

```sql

with subq_1 as (SELECT count(order_id) as orders_count,
                       user_id
                FROM   user_actions
                WHERE  action = 'create_order'
                GROUP BY user_id)
SELECT round(avg(orders_count), 2) as orders_avg
FROM   subq_1

```

#### [Задача 3](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257120)
Выведите из таблицы `products` информацию о всех товарах кроме самого дешёвого.

Результат отсортируйте по убыванию `id` товара.

Поля в результирующей таблице: `product_id, name, price`

```sql

with subq_1 as (SELECT min(price) as min_price
                FROM   products)
SELECT *
FROM   products
WHERE  price != (SELECT min_price
                 FROM   subq_1)
ORDER BY product_id desc

```


#### [Задача 4](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257124)
Выведите информацию о товарах в таблице `products`, цена на которые превышает среднюю цену всех товаров на 20 рублей и более. Результат отсортируйте по убыванию `id` товара.

Поля в результирующей таблице: `product_id, name, price`

```sql

with subq_1 as (SELECT avg(price)+20 as avg_price
                FROM   products)
SELECT *
FROM   products
WHERE  price > (SELECT avg_price
                FROM   subq_1)
ORDER BY product_id desc

```

#### [Задача 5](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257125)
Посчитайте количество уникальных клиентов в таблице `user_actions`, сделавших за последнюю неделю хотя бы один заказ.

Полученную колонку с числом клиентов назовите `users_count`. В качестве текущей даты, от которой откладывать неделю, используйте последнюю дату в той же таблице `user_actions`.

Поле в результирующей таблице: `users_count`



```sql


```
