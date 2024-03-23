# KZ5
Проект KZ5 представляет собой приложение-сервер, разработанное для обучения моделей машинного обучения с последующим предоставлением результатов пользователю. Это веб-приложение предлагает пользователям возможность авторизации, выбора типа задачи (регрессия или классификация), заполнения формы с данными для обучения модели и запуска процесса обучения.

Для разработки приложения были использованы следующие технологии:
- Язык Go
  + Фреймворк `Gorilla/MUX` для удобного роутинга
  + Собственная библиотека `net/http` для обработки запросов
  + Собственная библиотека `database/sql` для работы с SQL запросами
  + Собственная библиотека `html/template` для обработки HTML шаблонов
- Язык SQL
- Язык HTML
- Docker и Docker Compose

## Структура проекта

1. **config**: Конфигурационные файлы.
2. **docker**: Файлы Docker.
3. **docs**: Документация проекта на swagger. (TODO часть)
4. **filestorage**: Хранилище файлов.
5. **initializr**: Инициализатор соединения с базой данных.
6. **models**: Модели оюъектов.
7. **python**: Файлы Python и класс для работы с ними.
8. **repository**: Классы для ряботы с бд.
9. **schema**: Схемы для инициализации бд.
10. **server**: Код сервера.
11. **web**: Файлы веб-интерфейса.
12. **docker-compose.yml**: файл сборки докер контейнеров

## Использование

### Установка

Зайдите в папку куда вы хотите установить проект и скачайте репозиторий:
```bash
# Клонировать репозиторий
git clone https://github.com/mihaff/kz5

# Перейти в папку проекта
cd kz5

# Установить зависимости
go mod tidy
```

### Запуск
1. Убедитесь что порты 8080 и 5432 свободны.
2. Запустите Docker контейнер: 
```bash
docker-compose up
```
3. Перейдите по адресу `http://localhost:8080/home` в вашем браузере.

### Сценарий использования

1. Заполните форму на сайте для авторизации/входа.
2. Выберите тип модели (регрессия/классификация) в верхней панели и перейдите на форму.
3. Заполните форму и запустите процесс обучения модели.
4. После успешного завершения обучения, вы увидите результаты и сможете скачать модель.

## Основные компоненты

### Серверная часть
Включает в себя логику обработки запросов пользователей, инициализацию обучения моделей и предоставление результатов обучения. Основной код находится в папке `server`, там же лежит `main.go` проекта.

Основные эндпоинты:

1. **/swagger/**: TODO

2. **/assets/**: Этот эндпоинт обрабатывает запросы для доступа к статическим ресурсам, таким как изображения, CSS и JavaScript файлы, хранящиеся в папке `/web/`.

3. **/home**: обрабатывает запросы для отображения главной страницы веб-приложения.

4. **/users/enter**: обрабатывает запросы для отображения страницы входа пользователя.

5. **/users/register**: обрабатывает запросы для отображения страницы регистрации пользователя.

6. **/profile**: обрабатывает запросы для отображения страницы профиля пользователя.

7. **/api/users/enter**: обрабатывает POST запросы для обработки данных входа пользователя.

8. **/api/users/register**: обрабатывает POST запросы для обработки данных регистрации пользователя.

9. **/api/profile**: обрабатывает запросы для работы с профилем пользователя. (TODO)

10. **/shipment/model_class**: обрабатывает запросы для отображения страницы формы обучения модели классификации.

11. **/shipment/model_reg**: обрабатывает запросы для отображения страницы формы обучения модели регрессии.

12. **/api/shipment/progress/{model_type}**: обрабатывает запросы для запуска прогресса обучения модели. Модель может быть как классификационной, так и регрессионной. Тип указывается через model_type

13. **/api/shipment/download_results/{shipment_id}**: обрабатывает запросы для загрузки результатов обучения модели по указанному `shipment_id`.

14. **/shipment/result/{shipment_id}**: обрабатывает запросы для отображения страницы сохранения обученной модели.


### Фронтенд

Веб-интерфейс для взаимодействия с пользователем, включающий главную сраницу, интерфейс авторизации, профиль, интерфейс выбора типа задачи и отображение результатов обучения. Все файлы находятся в папке `web`

### Python
Основная папка - `pyhton` которая содержит
- два файла твечающих за модели регрессии и классификации соответственно: `model_reg.py`, `model_class.py`
- pymodel.go: реализацию класса для запуска моделей и парсинга результатов
- requirements.txt: библиотеки для файлов питона. При необходимости локального запуска убедитесь что они установлены.

### SQL База данных
Основной код для взаимодействия с ней находится в `repository`. Модели сущностей описаны в папке `models`
Сама база данных инициализируется в `schema/init.up.sql` и описывает структуру нескольких таблиц для хранения данных, связанных с пользователями, отправками (shipment) моделей, скачанными файлами и метриками моделей. Вот краткое описание каждой таблицы:

1. **Таблица "users"**:
   - Содержит информацию о пользователях.
   - Поля:
     - user_id: уникальный идентификатор пользователя (автоинкрементируемый).
     - username: имя пользователя.
     - email: адрес электронной почты пользователя.
     - password: хешированный пароль пользователя.
     - created_at: дата и время создания записи (автоматически заполняется при создании новой записи).

2. **Таблица "shipments"**:
   - Хранит информацию о отправках моделей.
   - Поля:
     - shipment_id: уникальный идентификатор отправки (автоинкрементируемый).
     - user_id: идентификатор пользователя, который создал отправку.
     - modelType: тип модели (например, классификация или регрессия).
     - projectName: название проекта.
     - algorithm: алгоритм модели.
     - targetColumn: целевая колонка (для модели).
     - status: текущий статус отправки.
     - timestamp: дата и время создания записи (автоматически заполняется при создании новой записи).
     - Связь с таблицей "users" через поле user_id.

3. **Таблица "downloaded_files"**:
   - Содержит информацию о скачанных файлах, связанных с отправками моделей.
   - Поля:
     - file_id: уникальный идентификатор файла (автоинкрементируемый).
     - shipment_id: идентификатор отправки, к которой относится файл.
     - filepath: путь к файлу.
     - timestamp: дата и время создания записи (автоматически заполняется при создании новой записи).
     - Связь с таблицей "shipments" через поле shipment_id.

4. **Таблица "model_files"**:
   - Хранит информацию о файлах моделей, связанных с отправками.
   - Поля:
     - file_id: уникальный идентификатор файла (автоинкрементируемый).
     - shipment_id: идентификатор отправки, к которой относится файл.
     - filepath: путь к файлу.
     - timestamp: дата и время создания записи (автоматически заполняется при создании новой записи).
     - Связь с таблицей "shipments" через поле shipment_id.

5. **Таблица "model_metrics"**:
   - Содержит информацию о метриках моделей.
   - Поля:
     - metric_id: уникальный идентификатор метрики (автоинкрементируемый).
     - file_id: идентификатор файла модели, к которому относится метрика.
     - metric_name: название метрики.
     - metric_value: значение метрики.
     - Связь с таблицей "model_files" через поле file_id.

Эти таблицы представляют собой базовую структуру базы данных для хранения данных, связанных с отправками моделей машинного обучения и связанными с ними файлами и метриками.

### Хранилище файлов
Код и рекомендуемые при первоначальном запуске папки находятся в `repository`. Класс реализует примитивное хранилище данных разделяя файлы на `downloads` и `uploads` (для файлов моделей).
Пути к ним будут храниться в sql баз данных. Также обратите внимание что при сборке докера папки в которых будут храниться файлы обучения моделей связаны с локальными: `filestorage/uploads` `filestorage/downloads`. При желании вы можете удалить `volumes` из докера или заменить их.

## Контактная информация
Для связи с разработчиком проекта, обращайтесь к Polina Feklistova:

Email: pmfeklistova@edu.hse.ru

## Лицензия

Этот проект лицензирован в соответствии с условиями лицензии [LICENSE.md](LICENSE.md).
