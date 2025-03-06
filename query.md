Общее количество записей в таблице:
SELECT count()
FROM trips;
Query id: c3ade610-089d-4ff5-bbad-801026bf7db0

┌─count()─┐
│ 3000317 │
└─────────┘
Количество поездок с крупными чаевыми ($20+):
SELECT count() 
FROM trips 
WHERE tip_amount > 20;

Query id: 12345b49-49ab-4b3a-88b6-67414c9a9f69

┌─count()─┐
│    2317 │
└─────────┘
Средняя продолжительность поездки в минутах:
SELECT 
    avg(dateDiff('minute', pickup_datetime, dropoff_datetime)) AS avg_trip_duration_minutes
FROM trips;

┌─avg_trip_duration_minutes─┐
│        15.134790757109998 │
└───────────────────────────┘

Найти 5 самых популярных зон для начала поездки и вывести их названия вместе с количеством поездок
1.	Сгруппировать данные по pickup_ntaname.
2.	Посчитать количество поездок для каждой зоны.
3.	Отсортировать результат по убыванию количества поездок.
4.	Вывести топ-5 зон.
SELECT
    pickup_ntaname AS pickup_zone,
    count() AS trip_count
FROM trips
GROUP BY pickup_ntaname
ORDER BY trip_count DESC
LIMIT 5;

┌─pickup_zone────────────────────────────────┬─trip_count─┐
│ Midtown-Midtown South                      │     526864 │
│ Hudson Yards-Chelsea-Flatiron-Union Square │     288797 │
│ West Village                               │     210436 │
│ Turtle Bay-East Midtown                    │     197111 │
│ Upper East Side-Carnegie Hill              │     184327 │
└────────────────────────────────────────────┴────────────┘
топ-3 дня, в которые была наибольшая суммарная выручка
1.	Извлечь дату из pickup_datetime.
2.	Сгруппировать данные по дате.
3.	Посчитать суммарную выручку для каждого дня.
4.	Отсортировать результат по убыванию выручки.
5.	Вывести топ-3 дня.
toDate(pickup_datetime): Извлекает дату из временной метки pickup_datetime.
sum(total_amount): Считает суммарную выручку для каждого дня.
GROUP BY trip_date: Группирует данные по дате.
ORDER BY total_revenue DESC: Сортирует результат по убыванию выручки.
LIMIT 3: Ограничивает результат топ-3 днями.

SELECT
    toDate(pickup_datetime) AS trip_date,
    sum(total_amount) AS total_revenue
FROM trips
GROUP BY trip_date
ORDER BY total_revenue DESC
LIMIT 3;

┌──trip_date─┬──────total_revenue─┐
│ 2015-08-27 │ 1025374.9724099636 │
│ 2015-08-06 │    982360.76841259 │
│ 2015-08-13 │  957422.3683335185 │
└────────────┴────────────────────┘



