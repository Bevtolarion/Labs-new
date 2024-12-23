# Отчёт по лабораторной работе №4: Dockerfile
## Шаг 1. Создание папки для проекта и файлов 
```
mkdir my-docker-site
cd my-docker-site
```

Создаём файл index.html и Dockerfile (Я работаю в среде docker desktop) 
```
echo. > index.html
echo. > Dockerfile
```
Редактируем их:
```
notepad Dockerfile
notepad index.html
```
В index.html вносим:

<!doctype html>
<html>
  <head>
    <title>This is the title of the webpage!</title>
  </head>
  <body>
    <p>My one Dockerfile!!</p>
  </body>
</html>

```
В Dockerfile вносим:
# Используем базовый образ nginx
FROM nginx:latest

# Копируем наш сайт в стандартный каталог Nginx
COPY index.html /usr/share/nginx/html/index.html

# Указываем порт, на котором работает контейнер
EXPOSE 80
```
## Шаг 2. Установка образа и сборка. 
Перед сборкой скачаем образ, потому-что может не подгрузиться:
```
docker pull nginx:latest
```
Собираем образ:
```
docker build -t simple-website .
```
Проверяем на создание образа:
```
docker images
```
Запускаем контейнер:
```
docker run -d -p 8080:80 simple-website
```
Проверяем запущенные контейнеры:
```
docker ps
```
Проверка работоспособности: 
Переходим по localhost и видим, всё функционирует.
```

## Результат.
Одностраничный сайт успешно создан и развернут с использованием Docker.

№№ Вывод
Четвёртая лабораторная работа помогла разобраться в работе с dockerfile и узнать, что это в целом. 


