# Практика работы с MongoDB
## Шаги:
### Запуск MongoDB в Docker
' docker run -d --name mongodb -p 27017:27017 mongo '
### Загрузка данных в MongoDB
Скачиваем данные о листингах и отзывах AirBnB внутрь контейнера
docker cp ~/non_relational_db/mongo_hw/listingsAndReviews.json mongodb:/tmp/listingsAndReviews.json
Подключаемся к контейнеру MongoDB
'docker exec -it mongodb bash '
Импортируем данные в MongoDB
mongoimport --db airbnb --collection listingsAndReviews --file /tmp/listingsAndReviews.json --jsonArray
### Выполнение запросов 
