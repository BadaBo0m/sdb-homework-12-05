# Домашнее задание к занятию 12.5. «Индексы» - "Червяков Антон"

### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```sql
SELECT (SUM(index_length) / SUM(data_length))*100 percentage_of_indexes
FROM INFORMATION_SCHEMA.TABLES;
```

### Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
![Скриншот-1](https://github.com/BadaBo0m/sdb-homework-12-05/blob/main/images/1.png)

- перечислите узкие места;
```
Бесполезный перебор film и inventory. Отсутствует индекс на дату оплаты.
```
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

```
Добавил индекс на дату оплаты и сделал GROUP BY для оптимизации и удобства вывода
```

```sql
SELECT DISTINCT 
	concat(c.last_name ,' ',c.first_name),
	sum(p.amount)
FROM payment p
LEFT JOIN customer c on c.customer_id =p.customer_id  
LEFT JOIN rental r on r.rental_date = p.payment_date 
WHERE  p.payment_date >= '2005-07-30' and p.payment_date < DATE_ADD('2005-07-30', INTERVAL 1 DAY)
GROUP BY p.customer_id;
```
![Скриншот-2](https://github.com/BadaBo0m/sdb-homework-12-05/blob/main/images/2.png)

