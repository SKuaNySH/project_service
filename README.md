# Project Service

Project Service — микросервис CorporationX для управления проектами и связанными с ними сущностями.

## Технологии

- Java 17, Spring Boot 3 и Gradle
- PostgreSQL, Spring Data JPA и Liquibase
- Redis
- OpenFeign для взаимодействия с User Service и Payment Service
- AWS S3 SDK для работы с файлами
- Springdoc OpenAPI для документации REST API

## Возможности

- Создание, просмотр, обновление и фильтрация проектов.
- Управление вакансиями: создание, получение, обновление и поиск по названию и позиции.
- Управление стажировками, кампаниями, пожертвованиями, встречами и ресурсами.
- Проверка прав пользователя для операций с вакансиями.
- Мягкое удаление кампании.

## Запуск локально

Для работы приложения необходимы JDK 17, PostgreSQL и Redis. Для интеграционных функций также нужны доступные User Service, Payment Service и S3-совместимое хранилище. Liquibase применяет миграции базы данных при старте приложения.

Соберите приложение:

```powershell
.\gradlew.bat clean bootJar
```

Запустите JAR:

```powershell
java -jar build/libs/service.jar
```

По умолчанию приложение работает на порту `8082` с контекстным путём `/api`. Настройки PostgreSQL можно переопределить переменными окружения:

| Переменная | Значение по умолчанию |
| --- | --- |
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://localhost:5432/postgres` |
| `SPRING_DATASOURCE_USERNAME` | `user` |
| `SPRING_DATASOURCE_PASSWORD` | `password` |

Настройки Redis, S3 и адреса внешних сервисов находятся в `src/main/resources/application.yaml`. Для запуска в Docker укажите адреса, доступные из контейнерной сети.

## Docker

Сначала соберите JAR и затем создайте Docker-образ:

```powershell
.\gradlew.bat bootJar
docker build -t project-service .
```

Образ запускает `build/libs/service.jar` на Eclipse Temurin JDK 17 и предоставляет порт `8082`. Перед запуском контейнера настройте его подключения к PostgreSQL, Redis и необходимым внешним сервисам.

## REST API

Базовый URL: `http://localhost:8082/api`.

Примеры операций с вакансиями:

| Метод | Путь | Описание |
| --- | --- | --- |
| `POST` | `/api/vacancies` | Создать вакансию |
| `GET` | `/api/vacancies/{vacancyId}` | Получить вакансию по ID |
| `PUT` | `/api/vacancies/update/{vacancyId}` | Обновить вакансию |
| `POST` | `/api/vacancies/filter` | Найти вакансии по названию и/или позиции |

Для создания и обновления вакансии передавайте идентификатор пользователя в заголовке `x-user-id`. Для мягкого удаления кампании предусмотрен маршрут `PUT /api/campaigns/{campaignId}/softDelete`.

Swagger UI доступен по адресу `http://localhost:8082/api/swagger-ui/index.html`, спецификация OpenAPI — `http://localhost:8082/api/v3/api-docs`.
