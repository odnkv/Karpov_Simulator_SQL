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
SELECT
  COUNT(DISTINCT user_id) AS users_count
FROM user_actions
WHERE action = 'create_order' and time BETWEEN (SELECT MAX(time) FROM user_actions) - interval '1 week' 
AND (SELECT MAX(time) FROM user_actions)

```

#### [Задача 6](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257126)
С помощью функции `AGE` и агрегирующей функции снова определите возраст самого молодого курьера мужского пола в таблице `couriers`, но в этот раз при расчётах в качестве первой даты используйте последнюю дату из таблицы `courier_actions`.

Чтобы получить именно дату, перед применением функции `AGE` переведите последнюю дату из таблицы `courier_actions` в формат `DATE`, как мы делали в [этом задании](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250927/).

Возраст курьера измерьте количеством лет, месяцев и дней и переведите его в тип `VARCHAR`. Полученную колонку со значением возраста назовите `min_age`.

Поле в результирующей таблице: `min_age`

```sql
SELECT
min(AGE((SELECT MAX(time)::date FROM courier_actions), birth_date))::varchar as min_age
FROM couriers
WHERE sex = 'male'

```

#### [Задача 7](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257127)
Из таблицы `user_actions` с помощью подзапроса или табличного выражения отберите все заказы, которые не были отменены пользователями.

Выведите колонку с `id` этих заказов. Результат запроса отсортируйте по возрастанию `id` заказа.

Добавьте в запрос оператор `LIMIT` и выведите только первые 1000 строк результирующей таблицы.

Поле в результирующей таблице: `order_id`

```sql

with subq_1 as (SELECT order_id
                FROM   user_actions
                WHERE  action = 'cancel_order')
SELECT order_id
FROM   user_actions
WHERE  order_id not in (SELECT *
                        FROM   subq_1)
ORDER BY order_id asc limit 1000

```

#### [Задача 8](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257123)
Используя данные из таблицы `user_actions`, рассчитайте, сколько заказов сделал каждый пользователь и отразите это в столбце `orders_count`.

В отдельном столбце `orders_avg` напротив каждого пользователя укажите среднее число заказов всех пользователей, округлив его до двух знаков после запятой.

Также для каждого пользователя посчитайте отклонение числа заказов от среднего значения. Отклонение считайте так: число заказов «минус» округлённое среднее значение. Колонку с отклонением назовите `orders_diff`.

Результат отсортируйте по возрастанию `id` пользователя. Добавьте в запрос оператор `LIMIT` и выведите только первые 1000 строк результирующей таблицы.

Поля в результирующей таблице: `user_id, orders_count, orders_avg, orders_diff`

Пояснение:

В этой задаче можно использовать подзапрос, написанный в первых заданиях этого урока. Чтобы не пришлось дважды писать один и тот же подзапрос, можно использовать оператор WITH.

```sql

WITH 
subq_1 AS (SELECT 
COUNT(DISTINCT order_id) AS orders_count,
user_id
FROM user_actions
WHERE action = 'create_order'
GROUP BY user_id),

subq_2 AS (SELECT ROUND(AVG(orders_count),2) AS orders_avg FROM subq_1)

SELECT 
user_id, 
orders_count, 
(SELECT * FROM subq_2) AS orders_avg,
orders_count - (SELECT * FROM subq_2) AS orders_diff

FROM subq_1 
ORDER BY user_id 
LIMIT 1000

```

#### [Задача 9](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/257133)
Назначьте скидку 15% на товары, цена которых превышает среднюю цену на все товары на 50 и более рублей, а также скидку 10% на товары, цена которых ниже средней на 50 и более рублей. Цену остальных товаров внутри диапазона (среднее - 50; среднее + 50) оставьте без изменений. При расчёте средней цены, округлите её до двух знаков после запятой.

Выведите информацию о всех товарах с указанием старой и новой цены. Колонку с новой ценой назовите `new_price`.

Результат отсортируйте сначала по убыванию прежней цены в колонке `price`, затем по возрастанию `id` товара.

Поля в результирующей таблице: `product_id, name, price, new_price`

Пояснение:

Чтобы не пришлось дважды писать один и тот же подзапрос, рекомендуем в этой задаче использовать оператор `WITH`.

```sql

WITH 
subq_1 AS (SELECT ROUND(AVG(price),2) AS avg_price
FROM products)

SELECT 
product_id,
name,
price,

CASE 
    WHEN price >= (SELECT avg_price FROM subq_1)+50 THEN price*0.85
    WHEN price <= (SELECT avg_price FROM subq_1)-50 THEN price*0.9
    ELSE price
END AS new_price
FROM products 
ORDER BY price DESC, product_id ASC

```

#### [Задача 10](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353794)
Выясните, есть ли в таблице courier_actions такие заказы, которые были приняты курьерами, но не были созданы пользователями. Посчитайте количество таких заказов.

Колонку с числом заказов назовите `orders_count`.

Поле в результирующей таблице: `orders_count`

```sql

SELECT count(distinct order_id) as orders_count
FROM   courier_actions
WHERE  action = 'accept_order'
   and order_id not in (SELECT DISTINCT order_id
                     FROM   user_actions)

```

#### [Задача 10](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353794)
Выясните, есть ли в таблице courier_actions такие заказы, которые были приняты курьерами, но не были созданы пользователями. Посчитайте количество таких заказов.

Колонку с числом заказов назовите `orders_count`.

Поле в результирующей таблице: `orders_count`

```sql

SELECT count(distinct order_id) as orders_count
FROM   courier_actions
WHERE  action = 'accept_order'
   and order_id not in (SELECT DISTINCT order_id
                     FROM   user_actions)

```

#### [Задача 11](https://lab.karpov.courses/learning/152/module/1762/lesson/17928/53213/353795)
НОВАЯ ЗАДАЧА

Хорошо, проверку на наличие очевидных ошибок мы сделали. Теперь давайте копнём немного глубже.

Мы знаем, что в данных о действиях курьеров есть две отметки времени: время принятия и время доставки заказа. Но все ли из принятых заказов числятся в данных как доставленные? Вряд ли, потому что заказ, во-первых, могли отменить, а во-вторых, его могли просто не успеть доставить.

Давайте проверим, так ли это на самом деле. Для начала просто определим заказы, которые не доставили.

Задание:

Выясните, есть ли в таблице user_actions такие заказы, которые были приняты курьерами, но не были доставлены пользователям: у заказа нет записи с действием 'deliver_order' в таблице `courier_actions`. Посчитайте уникальное количество таких заказов: используйте count(distinct order_id).

Колонку с числом заказов назовите `orders_count`.

Поле в результирующей таблице: `orders_count`

```sql

SELECT count(distinct order_id) as orders_count
FROM   user_actions
WHERE  order_id not in (SELECT order_id
                        FROM   courier_actions
                        WHERE  action = 'deliver_order')

```


