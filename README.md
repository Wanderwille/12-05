# Домашнее задание по теме "Индексы" - Подус Сергей

## Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

## Ответ:

![Скриншот 1](https://github.com/Wanderwille/scrinshot/blob/main/ngotm.png)

## Задание 2

Выполните explain analyze следующего запроса:

select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)

from payment p, rental r, customer c, inventory i, film f

where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

Перечислите узкие места;

Оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

## Ответ:

Как я считаю узкие места это: 1. Возможно отсутсвуют индексы, это соответственно замедляет выполнение запросов к БД. 

2. Возможно фильтрация по дате, так как может потребоваться индекс, для того что бы поиск выполнялся быстрее. 

3. Использование DISTINCT может создавать временную таблицу для удаления дубликатов, что может быть затратным в случае большого объема данных.

![Скриншот 2](https://github.com/Wanderwille/scrinshot/blob/main/explain%20analyze.png)

Создадим индекс CREATE index payment_date on payment(payment_date);

И попробую выполнить оптимизированый запрос

```sql
select concat(c.last_name, ' ', c.first_name), sum(p.amount)
from payment p
inner join rental r on r.rental_date = p.payment_date
inner join customer c on c.customer_id = r.customer_id 
where p.payment_date between '2005-07-30 00:00:00' and '2005-07-30 23:59:59'
group by c.last_name, c.first_name, c.customer_id  
```

![Скриншот 3](https://github.com/Wanderwille/scrinshot/blob/main/explain2%20.png)
