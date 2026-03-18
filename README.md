# La Maison

Fullstack приложение для цифровизации и автоматизации работы ресторана. Система включает в себя как работу с персоналом, так и с посетителями.

## Оглавление

- [1. О проекте](#1-о-проекте)
- [2. Стек технологий](#2-стек-технологий)
- [3. Вкладки и разделы приложения](#3-вкладки-и-разделы-приложения)
- [4. Фичи приложения](#4-фичи-приложения)
- [5. Запуск](#5-запуск)
- [6. Скрины](#6-скрины)

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

## 5. Запуск

### Шаг 1. Клонирование

Рекомендуемый вариант (сразу с сабмодулями):

```bash
git clone --recurse-submodules <REPO_URL>
cd la-maison
```

Если репозиторий уже клонирован без сабмодулей:

```bash
git submodule update --init --recursive
```

### Шаг 2. Установка зависимостей

```bash
cd backend
npm install

cd ../frontend
npm install
```

### Шаг 3. Настройка backend .env

Создайте файл backend/.env:

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

### Шаг 4. Поднять PostgreSQL

```bash
cd backend
docker-compose up -d
```

### Шаг 5. Prisma

```bash
cd backend
npx prisma migrate dev
npx prisma generate
```

Опционально заполнить тестовыми данными:

```bash
cd backend
npx ts-node prisma/seed.prod.ts
```

### Шаг 6. Запустить приложения

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

## 6. Скрины

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
