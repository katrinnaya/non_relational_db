# Практика работы с ClickHouse 

## Шаг 1. Запуск контейнер с ClickHouse с помощью Docker:
```
docker run --ulimit nofile=262144:262144 --name clickhouse-server -d \
  -p 8123:8123 -p 9000:9000 -v $(pwd)/data:/var/lib/clickhouse yandex/clickhouse-server
```
#### Параметры:
* ``` --ulimit nofile ``` увеличивает лимит открытых файлов, который важен для ClickHouse.
*	``` --name clickhouse-server ``` имя контейнера.
* ``` -d ``` запуск контейнера в фоновом режиме.
*	``` -p 8123:8123 ``` отображение порта HTTP-интерфейса ClickHouse.
*	``` -p 9000:9000 ``` отображение порта TCP-сервера ClickHouse.
*	``` -v $(pwd)/data:/var/lib/clickhouse ``` монтирование локальной папки ``` /data ``` в контейнер для хранения данных.
## Шаг 2. Подключение ClickHouse с помощью клиента (запуск внутри контейнера)
``` docker exec -it clickhouse-server clickhouse-client ``` 
## Шаг 3. Создание базы данных
Необязательный шаг (можно пропустить, и тогда вместо вашей БД будущая таблица создастся в дефолтной БД ``` default DATABASE ```
``` CREATE DATABASE taxi_data; ```
## Шаг 4. Создание таблицы
#### Запрос 1 (на игнорирование неизвестных полей)
``` SET input_format_skip_unknown_fields=1; ```
#### Запрос 2
```
INSERT INTO trips
SELECT
    trip_id,
    pickup_datetime,
    dropoff_datetime,
    pickup_longitude,
    pickup_latitude,
    dropoff_longitude,
    dropoff_latitude,
    passenger_count,
    trip_distance,
    fare_amount,
    extra,
    tip_amount,
    tolls_amount,
    total_amount,
    payment_type,
    pickup_ntaname,
    dropoff_ntaname
FROM s3(
    'https://datasets-documentation.s3.eu-west-3.amazonaws.com/nyc-taxi/trips_{0..2}.gz',
    'TabSeparatedWithNames',
    'trip_id String, pickup_datetime DateTime, dropoff_datetime DateTime, pickup_longitude Float64, pickup_latitude Float64, dropoff_longitude Float64, dropoff_latitude Float64, passenger_count UInt8, trip_distance Float64, fare_amount Float64, extra Float64, tip_amount Float64, tolls_amount Float64, total_amount Float64, payment_type String, pickup_ntaname String, dropoff_ntaname String',
    'gzip'
);
```
## Шаг 5. Загрузка данных
После создания таблицы мы можем загрузить данные из файлов, расположенных в облачном хранилище (например, Google Cloud Storage или Amazon S3). Используем функцию ``` gcs() ``` для загрузки данных из Google Cloud Storage.
```
INSERT INTO trips
SELECT
    trip_id,
    pickup_datetime,
    dropoff_datetime,
    pickup_longitude,
    pickup_latitude,
    dropoff_longitude,
    dropoff_latitude,
    passenger_count,
    trip_distance,
    fare_amount,
    extra,
    tip_amount,
    tolls_amount,
    total_amount,
    payment_type,
    pickup_ntaname,
    dropoff_ntaname
FROM gcs(
    'https://storage.googleapis.com/clickhouse-public-datasets/nyc-taxi/trips_{0..2}.gz',
    'TabSeparatedWithNames'
);    'TabSeparatedWithNames'
);
```
## Шаг 6. Ответа на задания
Запросы и вывод см. в ``` query.md ``` 
