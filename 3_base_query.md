### Практика

#### [Задача 1](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250919)

Выведите все записи из таблицы `products`.

Поля в результирующей таблице: `product_id, name, price`

```sql

SELECT *
FROM   products

```

#### [Задача 2](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250920)

Выведите все записи из таблицы `products`, отсортировав их по наименованиям товаров в алфавитном порядке, т.е. по возрастанию. Для сортировки используйте оператор `ORDER BY`.

Поля в результирующей таблице: `product_id, name, price`

```sql

SELECT *
FROM   products
ORDER BY name asc

```

#### [Задача 3](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250921)

Отсортируйте таблицу `courier_actions` сначала по колонке `courier_id` по возрастанию `id` курьера, потом по колонке `action` (снова по возрастанию), а затем по колонке `time`, но уже по убыванию — от самого последнего действия к самому первому. Не забудьте включить в результат колонку `order_id`.

Добавьте в запрос оператор `LIMIT` и выведите только первые 1000 строк результирующей таблицы.

Поля в результирующей таблице: `courier_id, order_id, action, time`

```sql

SELECT *
FROM   courier_actions
ORDER BY courier_id asc, action asc, time desc limit 1000

```

#### [Задача 4](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250922)

Используя операторы `SELECT, FROM, ORDER BY и LIMIT`, определите 5 самых дорогих товаров в таблице `products`, которые доставляет наш сервис. Выведите их наименования и цену.

Поля в результирующей таблице: `name, price`

```sql

SELECT name,
       price
FROM   products
ORDER BY price desc limit 5

```

#### [Задача 5](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250923)

Как в прошлом задании определите 5 самых дорогих товаров в таблице products. Но теперь колонки `name и price` переименуйте соответственно в `product_name и product_price`.

Поля в результирующей таблице: `product_name, product_price`

```sql

SELECT name as product_name,
       price as product_price
FROM   products
ORDER BY price desc limit 5

```
