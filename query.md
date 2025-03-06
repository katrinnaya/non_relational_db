## 1. Общее количество записей в таблице (сколько всего поездок):
```
SELECT count()
FROM trips;
``` 
```
┌─count()─┐
│ 3000317 │
└─────────┘
```
## 2. Количество поездок с крупными чаевыми ($20+):
```
SELECT count() 
FROM trips 
WHERE tip_amount > 20;
```
```
┌─count()─┐
│    2317 │
└─────────┘
```
## 3. Средняя продолжительность поездки в минутах:
```
SELECT 
    avg(dateDiff('minute', pickup_datetime, dropoff_datetime)) AS avg_trip_duration_minutes
FROM trips;
```
```
┌─avg_trip_duration_minutes─┐
│        15.134790757109998 │
└───────────────────────────┘
```
## 4. 5 самых популярных зон для начала поездки и вывод их названий вместе с количеством поездок
### 1.	Сгруппировать данные по ``` pickup_ntaname ```
### 2.	Посчитать количество поездок для каждой зоны
### 3.	Отсортировать результат по убыванию количества поездок
### 4.	Вывести топ-5 зон
```
SELECT
    pickup_ntaname AS pickup_zone,
    count() AS trip_count
FROM trips
GROUP BY pickup_ntaname
ORDER BY trip_count DESC
LIMIT 5;
```
```
┌─pickup_zone────────────────────────────────┬─trip_count─┐
│ Midtown-Midtown South                      │     526864 │
│ Hudson Yards-Chelsea-Flatiron-Union Square │     288797 │
│ West Village                               │     210436 │
│ Turtle Bay-East Midtown                    │     197111 │
│ Upper East Side-Carnegie Hill              │     184327 │
└────────────────────────────────────────────┴────────────┘
```
## 5. Топ-3 дня, в которые была наибольшая суммарная выручка
### 1.	Извлечь дату из ``` pickup_datetime ```
### 2.	Сгруппировать данные по дате
### 3.	Посчитать суммарную выручку для каждого дня
### 4.	Отсортировать результат по убыванию выручки
### 5.	Вывести топ-3 дня

``` toDate(pickup_datetime) ``` Извлекает дату из временной метки pickup_datetime
```
SELECT
    toDate(pickup_datetime) AS trip_date,
    sum(total_amount) AS total_revenue
FROM trips
GROUP BY trip_date
ORDER BY total_revenue DESC
LIMIT 3;
```
```
┌──trip_date─┬──────total_revenue─┐
│ 2015-08-27 │ 1025374.9724099636 │
│ 2015-08-06 │    982360.76841259 │
│ 2015-08-13 │  957422.3683335185 │
└────────────┴────────────────────┘
```



