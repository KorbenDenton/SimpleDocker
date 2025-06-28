## Part 1. Готовый докер

- Скачиваем образ nginx версии latest

![Alt text](<src/screenshots/Снимок экрана 2025-05-08 в 22.39.01.png>)

- Проверяем наличие образа

![Alt text](src/screenshots/image.png)

- Поднимаем контейнер nginx 

![Alt text](src/screenshots/image-1.png)

- Проверяем запущенные контейнеры

![Alt text](src/screenshots/image-2.png)

- При помощи команды `docker inspect` собираем информацию 

![Alt text](src/screenshots/image-3.png)

1) Размер - Size: 3.281kB
2) Замапленные порты - Ports: 80/tcp
3) ip контейнера - IPAddress: 172.17.0.2

- Останавливаем контейнер 

![Alt text](src/screenshots/image-4.png)

- Проверяем, запущен ли контейнер теперь

![Alt text](src/screenshots/image-5.png)

- Поднимаем контейнер с портами 80 и 443, замапленными на такие же порты на локальной машине и сразу же проверяем командой  `docker ps`

![Alt text](<src/screenshots/Снимок экрана 2025-05-09 в 19.01.54.png>)

- Убеждаемся, что nginx доступен в браузере 

![Alt text](src/screenshots/image-6.png)

- Перезапускаем контейнер и убеждаемся, что он запущен 

![Alt text](src/screenshots/image-8.png)

## Part 2. Операции с контейнером

- Смотрим nginx.conf внутри контейнера

![Alt text](src/screenshots/image-7.png)

- Создаем на локальной машине кастомный nginx.conf и настраиваем в нем отдачу статуса по пути /status 

![Alt text](src/screenshots/image-9.png)

- Копируем созданный nginx.conf внутрь контейнера

![Alt text](src/screenshots/image-10.png)

- Перезапускаем nginx внутри контейнера

![Alt text](src/screenshots/image-11.png)

- Проверяем, что по localhost:80/status отдается страничка со статусом сервера nginx

![Alt text](src/screenshots/image-12.png)

- Экспортируем контейнер в файл container.tar и останавливаем его

![Alt text](src/screenshots/image-14.png)

- Удаляем образ, не удаляя перед этим контейнеры

![Alt text](src/screenshots/image-15.png)
Ошибка. Удалить образ, не удалив перед этим все работающие и остановленные контейнеры на основе этого образа, по умолчанию нельзя. Можно либо удалить образ, применив флаг -f, либо предварительно удалить все контейнеры. Воспользуемся вторым вариантом.

![Alt text](src/screenshots/image-16.png)

- Импортируем контейнер обратно 

![Alt text](src/screenshots/image-17.png)

- Запускаем импортированный контейнер 

![Alt text](src/screenshots/image-18.png)

- Снова проверяем, что по localhost:80/status отдается страничка со статусом сервера nginx

![Alt text](src/screenshots/image-19.png)

## Part 3. Мини веб-сервер

- Делаем мини-сервер на C и FastCgi, который будет возвращать простейшую страничку с надписью `Hello World!`

<src/screenshots/a id="1"><src/screenshots//a>

![Alt text](src/screenshots/image-20.png)

- Запускаем написанный мини-сервер через spawn-fcgi на порту 8080, предварительно скомпилировав Си-файл

<src/screenshots/a id="2"><src/screenshots//a>

![Alt text](src/screenshots/image-21.png)

- Пишем свой nginx.conf, который будет проксировать все запросы с 81 порта на 127.0.0.1:8080.

<src/screenshots/a id="3"><src/screenshots//a>

![Alt text](src/screenshots/image-26.png)

- Запускаем nginx с написанной конфигурацией 

![Alt text](src/screenshots/image-23.png)

- Смотрим, что в браузере по localhost:81 отдается написанная страничка.

![Alt text](src/screenshots/image-24.png)

- Смотрим, что файл nginx.conf лежит по пути ./nginx/nginx.conf

![Alt text](src/screenshots/image-25.png)

## Part 4. Свой докер

- Пишем докерфайл, который:
1) собирает исходники мини сервера на FastCgi из [Части 3](#1);
2) запускает его на 8080 порту, как в [Части 3](#2);
3) копирует внутрь образа написанный ./nginx/nginx.conf из [Части 3](#3);
4) запускает nginx.

<src/screenshots/a id="4"><src/screenshots//a>

![Alt text](src/screenshots/image-27.png)

- Собираем созданный докерфайл

![Alt text](src/screenshots/image-28.png)

- Проверяем через `docker src/screenshots/images` наличие образа

![Alt text](src/screenshots/image-29.png)

- Запускаем собранный докер-образ с маппингом 81 порта на 80 на локальной машине и маппингом конфигурации ./nginx/nginx.conf внутрь контейнера по адресу, где лежат конфигурационные файлы nginx

![Alt text](src/screenshots/image-32.png)
 
- Смотрим, что по адресу http://localhost:80 отдается страничка написанного сервера

![Alt text](src/screenshots/image-31.png)

- Дописываем в ./nginx/nginx.conf проксирование странички /status, по которой nginx будет отдавать статус сервера

![Alt text](src/screenshots/image-33.png)

- Перезапускаем контейнер

![Alt text](src/screenshots/image-34.png)

- Смотрим, что теперь по localhost:80/status отдается страничка со статусом сервера

![Alt text](src/screenshots/image-35.png)

## Part 5. Dockle

- Прогоняем [образ из предыдущего задания](#4) через `dockle`

![alt text](src/screenshots/image-36.png)

<src/screenshots/a id="5"><src/screenshots//a>

- Исправляем докерфайл 

![alt text](src/screenshots/image-37.png)

- Заново собираем и запускаем

![alt text](src/screenshots/image-39.png)

- Снова прогоняем через `dockle`

![alt text](src/screenshots/image-38.png)

## Part 6. Базовый Docker Compose

- Пишем файл docker-compose.yml, который:

1) Поднимает докер-контейнер из [Части 5](#5).
2) Поднимает докер-контейнер с nginx, который будет проксировать все запросы с 8080 порта на 81 порт первого контейнера.
3) Маппит 8080 порт второго контейнера на 80 порт локальной машины.

![alt text](src/screenshots/image-40.png)

- Останавливаем и удаляем все запущенные контейнеры

![alt text](src/screenshots/image-41.png)

- Собираем и запускаем `docker-compose`

![alt text](src/screenshots/image-42.png)

- Смотрим, что в браузере по localhost:80 отдается написанная страничка, как и ранее

![alt text](src/screenshots/image-43.png)