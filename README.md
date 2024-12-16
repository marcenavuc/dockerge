# Лаба 2

Сервисы:
1. Сервис db (PostgreSQL)
	*	Образ: postgres:15 Используется PostgreSQL версии 15.
	*	Перезапуск: always Контейнер автоматически перезапускается при сбоях.
	*	Переменные окружения:
	*	POSTGRES_USER: задаётся через переменную DB_USER или по умолчанию user.
	*	POSTGRES_PASSWORD: задаётся через переменную DB_PASSWORD или по умолчанию password.
	*	POSTGRES_DB: задаётся через переменную DB_NAME или по умолчанию spicyragdb.
	*	Порты:Порт 5432 PostgreSQL маппируется на порт 5434 хоста.
	*	Тома:postgres_data:/var/lib/postgresql/data — для сохранения данных базы вне контейнера. data:/docker-entrypoint-initdb.d — для скриптов инициализации базы данных.
	*	Сеть: подключён к сети network.

2. Сервис elasticsearch

	*	Образ: docker.elastic.co/elasticsearch/elasticsearch:7.9.3. Используется Elasticsearch версии 7.9.3.
	*	Переменные окружения:
	*	discovery.type=single-node — Elasticsearch работает в режиме одиночной ноды.
	*	ES_JAVA_OPTS=-Xms1g -Xmx1g — выделение 1 ГБ памяти для Java Heap.
	*	Порты:
	*	9200:9200 — основной порт HTTP API Elasticsearch.
	*	9300:9300 — порт для внутреннего взаимодействия нод.
	*	Тома:
es_data:/usr/share/elasticsearch/data — для сохранения данных вне контейнера.
	*	Сеть: подключён к сети network.

3. Сервис interface (FastAPI)
	*	Назначение: Backend на FastAPI, запускаемый через Uvicorn.
	*	Сборка. Собирается из текущего контекста (build: .).
	*	Команда запуска:
	*	Ожидается доступность PostgreSQL (через pg_isready) и Elasticsearch (через curl).
	*	После готовности зависимостей запускается сервер Uvicorn на 0.0.0.0:8000 с поддержкой перезагрузки кода (--reload).
	*	Порты: Порт 8000 маппируется на хост.
	*	Зависимости: Зависит от сервиса db.
	*	Тома: ./interface:/app/interface — локальная папка с кодом монтируется в контейнер.
	*	Сеть: подключён к сети network.
	*	Healthcheck: Проверяет доступность приложения по адресу http://localhost:8000:
	*	Интервал: каждые 30 секунд.
	*	Тайм-аут: 10 секунд.
	*	Повторные попытки: 5 раз.

4. Сервис streamlit
	*	Назначение: Приложение для визуализации данных на Streamlit.
	*	Сборка: Собирается из текущего контекста (build: .).
	*	Команда запуска: Запускается Streamlit-приложение streamlit/app.py на порту 8501.
	*	Порты: Порт 8501 маппируется на хост.
	*	Зависимости: Зависит от сервиса interface.
	*	Сеть: подключён к сети network.

Тома (volumes)
	*	postgres_data: Для хранения данных PostgreSQL.
	*	es_data: Для хранения данных Elasticsearch.

Сети (networks)
	*	network: Общая сеть для взаимодействия всех сервисов.


### Запуск
```bash
docker compose up -d
```


### Можно ли ограничивать ресурсы (например, память или CPU) для сервисов в docker-compose.yml? Если нет, то почему, если да, то как
Да, в docker-compose.yml можно ограничивать ресурсы для сервисов, ram, cpu для каждого сервиса. Пример:
```
services:
  interface:
    ...
    deploy:
      resources:
        limits: - сколько ресурсов максимально можно использовать
          memory: 2048M
          cpus: 4
        reservations: - сколько ресурсов минимально надо использовать
          memory: 512M
          cpus: 1
```

### Как можно запустить только определенный сервис из docker-compose.yml, не запуская остальные?
```bash
docker compose up <service-name>
docker compose up db # пример
```
