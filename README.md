# Kittygram Final

[![CI/CD Status](https://github.com/Kotpilota/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/Kotpilota/kittygram_final/actions)
[![Docker](https://img.shields.io/badge/docker-available-blue)](https://hub.docker.com/u/kotpilota)

**Kittygram** — это веб-приложение для любителей кошек, где пользователи могут делиться фотографиями своих питомцев и рассказывать об их достижениях. Проект демонстрирует современные подходы к разработке с использованием контейнеризации и автоматизированного деплоя.

## О проекте

Этот проект является финальным заданием курса "Python-разработчик" от Яндекс.Практикум и показывает полный цикл разработки веб-приложения:

- **Цель**: Создание платформы для обмена фотографиями котиков с их достижениями
- **Задача**: Настройка CI/CD процессов, контейнеризация и автоматический деплой
- **Результат**: Готовое к продакшену приложение с современной инфраструктурой

### Основной функционал
- Регистрация и аутентификация пользователей
- Загрузка фотографий котиков с поддержкой base64
- Указание цветов с валидацией через webcolors
- Система достижений для питомцев
- Адаптивный дизайн для всех устройств
- Пагинация списка котиков

## Технологический стек

### Backend
- **Python 3.9** - основной язык разработки
- **Django 3.2.3** - веб-фреймворк
- **Django REST Framework 3.12.4** - API
- **Djoser 2.1.0** - аутентификация
- **PostgreSQL 13** - база данных
- **Gunicorn** - WSGI сервер

### Frontend  
- **React** - пользовательский интерфейс
- **Node.js 18** - среда выполнения

### DevOps & Infrastructure
- **Docker & Docker Compose** - контейнеризация
- **Nginx** - веб-сервер и reverse proxy
- **GitHub Actions** - CI/CD автоматизация
- **Docker Hub** - реестр образов

## Быстрый старт

### Требования
- Docker и Docker Compose
- Git

### Локальный запуск

1. **Клонируйте репозиторий:**
```bash
git clone https://github.com/Kotpilota/kittygram_final.git
cd kittygram_final
```

2. **Создайте файл окружения:**
```bash
cp .env.example .env
```

Заполните `.env` файл:
```env
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=secure_password_here
DB_HOST=db
DB_PORT=5432
SECRET_KEY=your-super-secret-django-key
DEBUG=False
ALLOWED_HOSTS=127.0.0.1,localhost,yourdomain.com
```

3. **Запустите проект:**
```bash
docker compose up -d
```

4. **Создайте суперпользователя (опционально):**
```bash
docker compose exec backend python manage.py createsuperuser
```

**Готово!** Приложение доступно по адресу `http://localhost:9000`

> **Примечание:** Миграции базы данных и сбор статических файлов выполняются автоматически при запуске backend контейнера благодаря entrypoint.sh скрипту.

## CI/CD Pipeline

Проект настроен на автоматический деплой через GitHub Actions. При каждом push в ветку `main`:

### Этап 1: Тестирование
- **Линтинг** - проверка кода с flake8
- **Backend тесты** - Django unit tests с PostgreSQL
- **Frontend тесты** - React компонентов

### Этап 2: Сборка и публикация
Создание и публикация образов в Docker Hub:
- `kotpilota/kittygram_backend:latest`
- `kotpilota/kittygram_frontend:latest`
- `kotpilota/kittygram_gateway:latest`

### Этап 3: Деплой
- Копирование `docker-compose.production.yml` на сервер
- Обновление контейнеров из новых образов
- Очистка неиспользуемых образов

### Этап 4: Уведомления
Отправка сообщения в Telegram об успешном деплое

## Настройка деплоя

### GitHub Secrets
Добавьте следующие секреты в настройки репозитория:

```
DOCKER_USERNAME      # Логин Docker Hub
DOCKER_PASSWORD      # Пароль Docker Hub  
HOST                 # IP адрес сервера
USER                 # Пользователь на сервере
SSH_KEY              # Приватный SSH ключ
SSH_PASSPHRASE       # Пароль SSH ключа (опционально)
TELEGRAM_TO          # ID чата Telegram
TELEGRAM_TOKEN       # Токен Telegram бота
```

### Подготовка сервера
1. Установите Docker и Docker Compose
2. Настройте SSH доступ
3. Создайте директорию для проекта
4. Скопируйте `.env` файл с production настройками

## API Documentation

### Аутентификация
```
POST /api/users/                 # Регистрация
POST /api/auth/token/login/      # Получение токена
POST /api/auth/token/logout/     # Выход
```

### Котики  
```
GET    /api/cats/                # Список котиков (пагинация)
POST   /api/cats/                # Добавить котика
GET    /api/cats/{id}/           # Детали котика
PUT    /api/cats/{id}/           # Обновить котика
DELETE /api/cats/{id}/           # Удалить котика
```

### Достижения
```
GET  /api/achievements/          # Список достижений
POST /api/achievements/          # Создать достижение
```

## Архитектура проекта

```
kittygram_final/
├── backend/                    # Django REST API
│   ├── cats/                   # Модели котиков и достижений
│   ├── kittygram_backend/      # Настройки проекта
│   ├── Dockerfile              # Backend образ
│   └── requirements.txt        # Python зависимости
│
├── frontend/                   # React приложение  
│   ├── src/                    # Исходники фронтенда
│   ├── Dockerfile              # Frontend образ
│   └── package.json            # Node.js зависимости
│
├── nginx/                      # Веб-сервер
│   ├── nginx.conf              # Конфигурация Nginx
│   └── Dockerfile              # Gateway образ
│
├── docker-compose.yml          # Локальная разработка
├── docker-compose.production.yml  # Продакшн
├── kittygram_workflow.yml      # CI/CD конфигурация
└── tests.yml                   # Конфигурация тестов
```

## Мониторинг и отладка

### Просмотр логов
```bash
# Все сервисы
docker compose logs -f

# Конкретный сервис
docker compose logs -f backend
docker compose logs -f frontend  
docker compose logs -f gateway
```

### Подключение к контейнеру
```bash
# Backend shell
docker compose exec backend python manage.py shell

# Bash в контейнере
docker compose exec backend bash
```

## Результаты проекта

- **Живая демонстрация**: [https://kittygrampilota.serveblog.net/](https://kittygrampilota.serveblog.net/)
- **Автоматизированный деплой** с полным CI/CD циклом
- **Контейнеризованная архитектура** готовая к масштабированию
- **Современный стек технологий** с лучшими практиками

---

<div align="center">

**Made with ❤️ for cat lovers**

[⭐ Star this repo](https://github.com/Kotpilota/kittygram_final/stargazers) | [🐛 Report Bug](https://github.com/Kotpilota/kittygram_final/issues) | [💡 Request Feature](https://github.com/Kotpilota/kittygram_final/issues)

</div>
