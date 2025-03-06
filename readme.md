# Практика работы с ClickHouse 

## Шаг 1. Установите ClickHouse на локальную машину:

Для Ubuntu/Debian:

bash
Copy
sudo apt-get install apt-transport-https ca-certificates dirmngr
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4
echo "deb https://repo.clickhouse.com/deb/stable/ main/" | sudo tee /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update
sudo apt-get install clickhouse-server clickhouse-client
## Шаг 2. Запустите ClickHouse
sudo service clickhouse-server start
## Шаг 3. Подключитесь к ClickHouse
clickhouse-client
## Шаг 4. Скачивание данных
## Шаг 5. Выполнение запросов
•	Посчитать общее количество записей в таблице (сколько всего поездок)
•	Найти, сколько поездок сопровождались «крупными» чаевыми (больше $20)
•	Определить среднюю продолжительность поездки в минутах
•	Найти 5 самых популярных зон для начала поездки и вывести их названия вместе с количеством поездок
•	Найти топ-3 дня, в которые была наибольшая суммарная выручка

