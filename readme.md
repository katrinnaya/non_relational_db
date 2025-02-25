# Практика работы с MongoDB
## Шаги:
### 1. Запуск MongoDB в Docker
``` docker run -d --name mongodb -p 27017:27017 mongo ```
### 2. Загрузка данных в MongoDB
#### 2.1 Скачиваем данные о листингах и отзывах AirBnB внутрь контейнера (файл ``` listingsAndReviews.json ```)
``` docker cp ~/non_relational_db/mongo_hw/listingsAndReviews.json mongodb:/tmp/listingsAndReviews.json ```
#### 2.2 Подключаемся к контейнеру MongoDB
``` docker exec -it mongodb bash ```
#### 2.3 Импортируем данные в MongoDB
``` mongoimport --db airbnb --collection listingsAndReviews --file /tmp/listingsAndReviews.json ``` 
### 3. Проверка данных 
#### 3.1 Подключаемся к MongoDB (внутри контейнера)
``` mongosh ```
#### 3.2 Подключаемся к созданоой базе данных
``` use airbnb ```
### Выполнение запросов 
Запросы и их результат представлены в файле ``` mongodb_requests ``` 


