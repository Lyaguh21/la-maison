# La Maison

Fullstack приложение для цифровизации и автоматизации работы ресторана. Система включает в себя как работу с персоналом, так и с посетителями.

## Оглавление

- [1. О проекте](#1-о-проекте)
- [2. Стек технологий](#2-стек-технологий)
- [3. Вкладки и разделы приложения](#3-вкладки-и-разделы-приложения)
- [4. Фичи приложения](#4-фичи-приложения)
- [5. Локальный запуск](#5-локальный-запуск)
- [6. Деплой на VPS](#6-деплой-на-vps)
- [7. Скрины](#7-скрины)

## 1. О проекте

La Maison — это CRM для ресторанного бизнеса которая покрывает основные процессы, а именно:

- авторизация и управление пользователями
- бронирования и работа со столами
- управление меню и аллергенами
- заказы, очередь приготовления и выдача
- дашборды по метрикам ресторана

## 2. Стек технологий

### Frontend

- React 19 + TypeScript
- Vite
- Mantine
- Redux Toolkit + RTK Query
- React Router
- Tabler Icons
- DnD Kit

### Backend

- NestJS 11
- Prisma 7 + PostgreSQL
- JWT + cookies
- Swagger
- class-validator / class-transformer
- Joi
- Docker Compose (PostgreSQL)

## 3. Вкладки и разделы приложения

### Публичная часть

- Главная
  - лендинг, с основной информацией ресторана
- Меню
  - просмотр меню ресторана, с удобными фильтрами, поиском, пагинацией
- Бронирование
  - создание брони в ресторан с выбором стола на 2D макете ресторана
- Профиль
  - заполнение контактной информации о себе, а так же аллергий
  - информация о настоящих и прошлых бронях, с возможностью просмотра чека, его оплаты, и заказа по меню, без ожидания официанта
- Авторизация
  - вход
  - регистрация

### Панель сотрудника

- Администратор
  - Дашборды: вся основная информация о ресторане в удобных графиках
  - Редактор схемы зала: создание схемы ресторана при помощи drag n drop
  - Управление пользователями: выдача ролей пользователям
- Официант
  - Бронирования: удобная работа с текущими и будущими бронями, возможность расположить гостя без брони
  - Заказы: создание заказов на конкретную бронь
  - Готовые блюда: просмотр готовых блюд с кухни

- Повар
  - Очередь заказов: просмотр заказов посетителей, с отображением их аллергий и предпочтений

## 4. Фичи приложения

- Создание макета ресторана на сетке посредством drag n drop
- Хранение фильтров меню в url, для возможности делится с другими пользователями
- Автоматизированное отображение аллергий пользователя в заказе
- Безопасная авторизация посредством httpOnly cookie jwt
- Адаптивный дизайн и темная тема

## 5. Локальный запуск

### 1) Клонирование

```bash
git clone --recurse-submodules <REPO_URL>
cd la-maison
```

Если клонировали без сабмодулей:

```bash
git submodule update --init --recursive
```

### 2) Установка зависимостей

```bash
cd backend && npm install
cd ../frontend && npm install
```

### 3) Запуск базы данных (локально)

```bash
cd ../backend
docker compose up -d
```

### 4) Настройка backend/.env

Создайте файл `backend/.env`:

```env
DATABASE_URL="postgresql://admin:admin@localhost:5432/backend_db"
PORT=3000

JWT_ACCESS_SECRET=access_secret_very_long
JWT_REFRESH_SECRET=refresh_secret_very_long
JWT_ACCESS_EXPIRES=15m
JWT_REFRESH_EXPIRES=7d

COOKIE_SECURE=false
COOKIE_SAMESITE=lax
```

### 5) Миграции и Prisma client

```bash
cd backend
npx prisma migrate dev
npx prisma generate
```

### 6) Запуск приложений

Backend:

```bash
cd backend
npm run start:dev
```

Frontend:

```bash
cd frontend
npm run dev
```

После запуска:

- Frontend: <http://localhost:5173>
- Backend API: <http://localhost:3000>
- Swagger: <http://localhost:3000/docs>

## 6. Деплой на VPS

### 1) Подготовка сервера

На VPS должны быть установлены:

- Docker + Docker Compose plugin
- Nginx
- Certbot (для SSL)

### 2) Клонирование проекта

```bash
git clone --recurse-submodules <REPO_URL>
cd la-maison
```

### 3) Настройка .env в корне проекта

Создайте файл `.env` рядом с `docker-compose.yml`:

```env
JWT_ACCESS_SECRET=very_long_random_secret
JWT_REFRESH_SECRET=very_long_random_secret
JWT_ACCESS_EXPIRES=15m
JWT_REFRESH_EXPIRES=7d

COOKIE_SECURE=false
COOKIE_SAMESITE=lax

RUN_SEED_PROD=false
CORS_ORIGIN=http://lyaguh.site
```

Для HTTPS после выпуска сертификата:

```env
COOKIE_SECURE=true
COOKIE_SAMESITE=none
CORS_ORIGIN=https://lyaguh.site
```

### 4) Сборка и запуск контейнеров

```bash
docker compose build --progress=plain
docker compose up -d
docker compose ps
```

### 5) Настройка Nginx (домен -> frontend контейнер)

В `docker-compose.yml` фронтенд публикуется на `8080:80`. Хостовый Nginx должен проксировать домен на `127.0.0.1:8080`.

Пример server-блока:

```nginx
server {
  listen 80;
  listen [::]:80;
  server_name lyaguh.site www.lyaguh.site;

  location / {
    proxy_pass http://127.0.0.1:8080;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

Проверка и перезагрузка Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### 6) Выпуск SSL сертификата

```bash
sudo certbot --nginx -d lyaguh.site -d www.lyaguh.site
```

Если `www` еще не указывает на VPS, сначала выпускайте только на корень:

```bash
sudo certbot --nginx -d lyaguh.site
```

### 7 Seed prod

Чтобы выполнить продовый сид один раз:

1. Установите в `.env`: `RUN_SEED_PROD=true`
2. Перезапустите backend:

```bash
docker compose up -d --build backend
```

3. Проверьте логи:

```bash
docker compose logs -f --tail=200 backend
```

4. Верните `RUN_SEED_PROD=false` и снова перезапустите backend.

## 7. Скрины

### Схема БД

![Схема](screens/DBscheme.png)

### Лендинг

![Лендинг 1](screens/landing-1.png)
![Лендинг 2](screens/landing-2.png)

### Меню

![Меню](screens/menu-1.png)

### Бронирование

![Бронирование](screens/booking-1.png)

### Профиль

![Профиль 1](screens/profile-1.png)
![Профиль 2](screens/profile-2.png)
![Авторизация](screens/auth-1.png)

### Панель админа

![Панель админа 1](screens/admin-1.png)
![Панель админа 2](screens/admin-2.png)
![Панель админа 3](screens/admin-3.png)

### Панель официанта

![Панель официанта 1](screens/waiter-1.png)
![Панель официанта 2](screens/waiter-2.png)
![Панель официанта 3](screens/waiter-3.png)

### Панель повара

![Панель повара](screens/cook-1.png)

### Адаптив

<p align="center"> 
  <img src="screens/adaptive-1.png" width="300px" />
  <img src="screens/adaptive-1.png" width="300px" />
</p>

### Темная тема

![Темная тема](screens/black-theme-1.png)
