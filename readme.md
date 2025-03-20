# Учет оценок студентов университета в MongoDB

## 1. Проектирование базы данных

База данных разработана для учета оценок студентов университета. Она позволяет хранить информацию о студентах, курсах и оценках, а также выполнять различные запросы для анализа данных.

Прежде чем планировать структуру данных, были изучены типы данных, поддерживаемые MongoDB:

- **String**: Строка (например, "Иван").
- **Number**: Число (например, 1 или 3.14).
- **Boolean**: Логическое значение (`true` или `false`).
- **Date**: Дата (например, `new Date("2023-10-01")`).
- **Array**: Массив (например, `["математика", "физика"]`).
- **Object**: Вложенный документ (например, `{ address: "ул. Ленина, 10" }`).
- **ObjectId**: Уникальный идентификатор (например, `ObjectId("654321abcdef123456789012")`).

Т.к. в MongoDB не нужно заранее определять атрибуты и их типы, данные были загружены в таблицы, и база данных сама определила структуру на основе вставленных документов.

Получившаяся структура базы данных представлена ниже.

## 2. Структура базы данных

База данных состоит из трех коллекций:

1. **Students** (Студенты)
2. **Courses** (Курсы)
3. **Grades** (Оценки)

### 2.1 Коллекция `Students`

Эта коллекция хранит информацию о студентах. Каждый документ содержит следующие поля:

- `_id`: Уникальный идентификатор студента (генерируется автоматически).
- `name`: Имя студента.
- `surname`: Фамилия студента.
- `group`: Группа, в которой учится студент.
- `faculty`: Факультет, на котором учится студент.
- `year_of_study`: Год обучения студента.

### 2.2 Коллекция `Courses`

Эта коллекция хранит информацию о курсах. Каждый документ содержит следующие поля:

- `_id`: Уникальный идентификатор курса (генерируется автоматически).
- `course_name`: Название курса.
- `lecturer`: Преподаватель, который ведет курс.
- `credits`: Количество кредитов за курс.

### 2.3 Коллекция `Grades`

Эта коллекция хранит информацию об оценках студентов. Каждый документ содержит следующие поля:

- `_id`: Уникальный идентификатор записи (генерируется автоматически).
- `student_id`: Ссылка на студента (`ObjectId` из коллекции `Students`).
- `course_id`: Ссылка на курс (`ObjectId` из коллекции `Courses`).
- `grade`: Оценка студента.
- `date`: Дата выставления оценки.

## 3. Схема базы данных

Для создания схемы базы данных был использован сервис [dbdiagram.io](https://dbdiagram.io/).

Запрос:

```
Table Students {
  _id ObjectId
  name String
  surname String
  group String
  faculty String
  year_of_study Number
}

Table Grades {
  _id ObjectId
  student_id ObjectId
  course_id ObjectId
  grade Number
  date Date
}

Table Courses {
  _id ObjectId
  course_name String
  lecturer String
  credits Number
}

Ref: Students._id < Grades.student_id
Ref: Courses._id < Grades.course_id
```

## 4. Обоснование структуры базы данных

### 4.1 Нормализация данных

База данных спроектирована с учетом принципов нормализации:

- Данные о студентах, курсах и оценках хранятся в отдельных коллекциях.
- Связи между коллекциями реализованы через ссылки (`student_id` и `course_id` в коллекции `Grades`). Каждая оценка (`Grades`) принадлежит конкретному студенту (`Students`) и конкретному курсу (`Courses`). `Grades` выступает в роли промежуточной таблицы, которая связывает студентов и курсы.
- Это позволяет избежать дублирования данных и упрощает их обновление.

### 4.2 Удобство для пользователей

База данных разработана с учетом потребностей различных пользователей:

- **Студенты** могут просматривать свои оценки.
- **Преподаватели** могут добавлять оценки и анализировать успеваемость студентов.
- **Деканат** может получать отчеты по успеваемости студентов и курсов.

## 5. Индексы

Для ускорения запросов созданы индексы на часто используемые поля:

- Индекс на `student_id` в коллекции `Grades`. Ускоряет поиск оценок конкретного студента.
- Индекс на `course_id` в коллекции `Grades`. Ускоряет поиск оценок по конкретному курсу.
- Индекс на `group` в коллекции `Students`. Ускоряет поиск студентов по группе.

## 6. Запуск проекта (через Docker)

### 6.1 Запускаем контейнер с MongoDB
``` docker run -d --name mongodb-new -p 28017:27017 mongo ```
### 6.2.	Подключаемся к контейнеру
``` docker exec -it mongodb-new bash ```
### 6.3.	Подключаемся к MongoDB (внутри контейнера)
``` mongosh ```
### 6.4.	Создаем базы данных
```
use university
switched to db university
university>
```
MongoDB автоматически создает базу данных, если она не существует.
### 6.5.	Создаем три коллекции: Students, Courses, и Grades
```
db.createCollection("Students");
university>
{ ok: 1 }

db.createCollection("Courses");
university>
{ ok: 1 }

db.createCollection("Grades");
university>
{ ok: 1 }
 ```
### 6.6.	Добавление тестовых данных
#### 6.6.1.	Добавляем студентов в коллекцию Students
``` 
db.Students.insertMany([
    {
        name: "Иван",
        surname: "Иванов",
        group: "ГРУППА-101",
        faculty: "Факультет информатики",
        year_of_study: 1
    },
    {
        name: "Мария",
        surname: "Петрова",
        group: "ГРУППА-102",
        faculty: "Факультет математики",
        year_of_study: 2
    },
    {
        name: "Алексей",
        surname: "Сидоров",
        group: "ГРУППА-101",
        faculty: "Факультет информатики",
        year_of_study: 1
    },
    {
        name: "Елена",
        surname: "Козлова",
        group: "ГРУППА-103",
        faculty: "Факультет физики",
        year_of_study: 3
    }
]);
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('67dad4a3af2a03271551e944'),
    '1': ObjectId('67dad4a3af2a03271551e945'),
    '2': ObjectId('67dad4a3af2a03271551e946'),
    '3': ObjectId('67dad4a3af2a03271551e947')
  }
}
```
#### 6.6.2.	Добавляем курсы в коллекцию Courses
```
db.Courses.insertMany([
    {
        course_name: "Базы данных",
        lecturer: "Петров П.П.",
        credits: 5
    },
    {
        course_name: "Математический анализ",
        lecturer: "Иванова И.И.",
        credits: 6
    },
    {
        course_name: "Физика",
        lecturer: "Сидоров С.С.",
        credits: 4
    },
    {
        course_name: "Программирование",
        lecturer: "Козлов К.К.",
        credits: 5
    }
]);
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('67dad4deaf2a03271551e948'),
    '1': ObjectId('67dad4deaf2a03271551e949'),
    '2': ObjectId('67dad4deaf2a03271551e94a'),
    '3': ObjectId('67dad4deaf2a03271551e94b')
  }
}
```
#### 6.6.3.	Добавляем оценки в коллекцию Grades
```
db.Grades.insertMany([
    {
        student_id: ObjectId('67dad4a3af2a03271551e944'), // Студент 1
        course_id: ObjectId('67dad4deaf2a03271551e948'),  // Курс 1
        grade: 4,
        date: new Date("2023-10-01")
    },
    {
        student_id: ObjectId('67dad4a3af2a03271551e945'), // Студент 2
        course_id: ObjectId('67dad4deaf2a03271551e949'),  // Курс 2
        grade: 5,
        date: new Date("2023-10-02")
    },
    {
        student_id: ObjectId('67dad4a3af2a03271551e946'), // Студент 3
        course_id: ObjectId('67dad4deaf2a03271551e948'),  // Курс 1
        grade: 3,
        date: new Date("2023-10-03")
    },
    {
        student_id: ObjectId('67dad4a3af2a03271551e947'), // Студент 4
        course_id: ObjectId('67dad4deaf2a03271551e94a'),  // Курс 3
        grade: 5,
        date: new Date("2023-10-04")
    }
]);
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('67dad5dcaf2a03271551e94c'),
    '1': ObjectId('67dad5dcaf2a03271551e94d'),
    '2': ObjectId('67dad5dcaf2a03271551e94e'),
    '3': ObjectId('67dad5dcaf2a03271551e94f')
  }
}
```
### 6.7.	Проверка данных
#### 6.7.1.	Проверяем, что студенты добавлены
``` db.Students.find({}); ```
#### 6.7.2.	Проверяем, что курсы добавлены
``` db.Courses.find({}); ```
#### 6.7.3.	Проверяем, что оценки добавлены
``` db.Grades.find({}); ```
### 6.8.	Добавление индексов
#### 6.8.1.	Добавляем индекс на student_id в коллекции Grades для быстрого поиска оценок студента
```
db.Grades.createIndex({ student_id: 1 });
university>
student_id_1
```
#### 6.8.2.	Добавляем индекс на course_id в коллекции Grades для быстрого поиска оценок по курсу
```
db.Grades.createIndex({ course_id: 1 });
university>
course_id_1
```
#### 6.8.3.	Добавляем индекс на group в коллекции Students для быстрого поиска студентов по группе
```
db.Students.createIndex({ group: 1 });
university>
group_1
```
## 7.	Примеры запросов
### 7.1. Получение всех студентов из определенной группы, например, "ГРУППА-101"
```
db.Students.find({ group: "ГРУППА-101" });
[
  {
    _id: ObjectId('67dad4a3af2a03271551e944'),
    name: 'Иван',
    surname: 'Иванов',
    group: 'ГРУППА-101',
    faculty: 'Факультет информатики',
    year_of_study: 1
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e946'),
    name: 'Алексей',
    surname: 'Сидоров',
    group: 'ГРУППА-101',
    faculty: 'Факультет информатики',
    year_of_study: 1
  }
]
```
### 7.2. Получение всех курсов, которые ведет конкретный преподаватель, например, "Петров П.П."
```
db.Courses.find({ lecturer: "Петров П.П." });
[
  {
    _id: ObjectId('67dad4deaf2a03271551e948'),
    course_name: 'Базы данных',
    lecturer: 'Петров П.П.',
    credits: 5
  }
]
```
### 7.3.	Получение всех оценок конкретного студента, например, "Иван Иванов"
```
const student = db.Students.findOne({ name: "Иван", surname: "Иванов" });
db.Grades.find({ student_id: student._id });
[
  {
    _id: ObjectId('67dad5dcaf2a03271551e94c'),
    student_id: ObjectId('67dad4a3af2a03271551e944'),
    course_id: ObjectId('67dad4deaf2a03271551e948'),
    grade: 4,
    date: ISODate('2023-10-01T00:00:00.000Z')
  }
]
```
### 7.4.	 Получение всех курсов, на которые записан конкретный студент, например,  "Иван Иванов"
```
const student = db.Students.findOne({ name: "Иван", surname: "Иванов" });
const grades = db.Grades.find({ student_id: student._id }).toArray(); // Преобразуем курсор в массив
const courseIds = grades.map(grade => grade.course_id); // Теперь это массив
db.Courses.find({ _id: { $in: courseIds } }); // Ищем курсы по массиву courseIds
[
  {
    _id: ObjectId('67dad4deaf2a03271551e948'),
    course_name: 'Базы данных',
    lecturer: 'Петров П.П.',
    credits: 5
  }
]
```
### 7.5.	Получение среднего балла конкретного студента, например, "Иван Иванов"
```
const student = db.Students.findOne({ name: "Иван", surname: "Иванов" });
db.Grades.aggregate([
    { $match: { student_id: student._id } },
    { $group: { _id: null, averageGrade: { $avg: "$grade" } } }
]);
[ { _id: null, averageGrade: 4 } ]
```
### 7.6.	 Получение среднего балла для всего списка студентов
```
db.Students.aggregate([
    {
        $lookup: {
            from: "Grades",
            localField: "_id",
            foreignField: "student_id",
            as: "grades"
        }
    },
    {
        $project: {
            name: 1,
            surname: 1,
            averageGrade: { $avg: "$grades.grade" }
        }
    }
]);
[
  {
    _id: ObjectId('67dad4a3af2a03271551e944'),
    name: 'Иван',
    surname: 'Иванов',
    averageGrade: 4.5
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e945'),
    name: 'Мария',
    surname: 'Петрова',
    averageGrade: 5
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e946'),
    name: 'Алексей',
    surname: 'Сидоров',
    averageGrade: 3
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e947'),
    name: 'Елена',
    surname: 'Козлова',
    averageGrade: 5
  },
  {
    _id: ObjectId('67dc4507af2a03271551e950'),
    name: Светлана,
    surname: 'Иванова',
    averageGrade: null
  }
]
```
### 7.7.	Добавление нового студента
```
db.Students.insertOne({
    name: "Светлана",
    surname: "Иванова",
    group: "ГРУППА-101",
    faculty: "Факультет информатики",
    year_of_study: 2
});
{
  acknowledged: true,
  insertedId: ObjectId('67dc4507af2a03271551e950')
}
```
### 7.8.	Добавление оценки «Отлично» для студента по курсы «Базы данных»
```
const course = db.Courses.findOne({ course_name: "Базы данных" });
const student = db.Students.findOne({ name: "Иван", surname: "Иванов" });

db.Grades.insertOne({
    student_id: student._id,
    course_id: course._id,
    grade: 5,
    date: new Date("2023-10-01")
});
{
  acknowledged: true,
  insertedId: ObjectId('67dc4582af2a03271551e951')
}
```
### 7.9.	Получение списка студентов, которые сдали курс "Базы данных" на "Отлично"
```
const course = db.Courses.findOne({ course_name: "Базы данных" });
db.Grades.aggregate([
    { $match: { course_id: course._id, grade: 5 } },
    { $lookup: { from: "Students", localField: "student_id", foreignField: "_id", as: "student" } },
    { $unwind: "$student" },
    { $project: { "student.name": 1, "student.surname": 1 } }
]);
[
  {
    _id: ObjectId('67dc4582af2a03271551e951'),
    student: { name: 'Иван', surname: 'Иванов' }
  }
]
```
### 7.10.	Получение всех студентов
```
db.Students.find({});
[
  {
    _id: ObjectId('67dad4a3af2a03271551e944'),
    name: 'Иван',
    surname: 'Иванов',
    group: 'ГРУППА-101',
    faculty: 'Факультет информатики',
    year_of_study: 1
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e945'),
    name: 'Мария',
    surname: 'Петрова',
    group: 'ГРУППА-102',
    faculty: 'Факультет математики',
    year_of_study: 2
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e946'),
    name: 'Алексей',
    surname: 'Сидоров',
    group: 'ГРУППА-101',
    faculty: 'Факультет информатики',
    year_of_study: 1
  },
  {
    _id: ObjectId('67dad4a3af2a03271551e947'),
    name: 'Елена',
    surname: 'Козлова',
    group: 'ГРУППА-103',
    faculty: 'Факультет физики',
    year_of_study: 3
  },
  {
    _id: ObjectId('67dc4507af2a03271551e950'),
    name: Светлана,
    surname: 'Иванова',
    group: 'ГРУППА-101',
    faculty: 'Факультет информатики',
    year_of_study: 2
  }
]
```
## 8.	Экспорт коллекций
Выполняем локально (не в контейнере)
### 8.1.	Экспортируем коллекцию Students
``` mongoexport --uri="mongodb://localhost:28017/university" --collection=Students --out=students.json ```
### 8.2.	Экспортируем коллекцию Courses
``` mongoexport --uri="mongodb://localhost:28017/university" --collection=Courses --out=courses.json ```
### 8.3.	Экспортируем коллекцию Grades
``` mongoexport --uri="mongodb://localhost:28017/university" --collection=Grades --out=grades.json ```
