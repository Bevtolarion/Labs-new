# Отчёт по лабораторной работе №3: Network, Registry, Portainer. Ресурсы
## Шаг 1. Удаление томов, контейнеров, образов. 
Останавливаем все контейнеры:
```
docker stop $(docker ps -a -q)
```

Находим ID контейнера Portainer
```
docker ps
```
Удаляем все кроме Portainer.

Удаляем остановленные контейнеры:
```
docker container rm $(docker ps -a -q)
```

Удаляем все образы
```
docker image prune -a
```

Удаляем все диски
```
docker volume rm $(docker volume ls -q)
```

Полностью чистим систему докера
```
docker system prune -a
```
## Шаг 2. Установка докер регистри от jc21/registry-ui
```
docker run -d -p 5000:5000 --name registry registry:2
```
Запускаем контейнер Docker Registry

```
docker run -d -p 8080:80 -e REGISTRY_TITLE="My Registry" -e REGISTRY_URL=http://localhost:5000 jc21/registry-ui
```
Добавляем jc21/registry-ui

```
docker run -d --name shipyard -p 8080:8080 -e DOCKER_HOST=tcp://docker:2375 shipyard/shipyard
```
Мы успешно загрузили docker registry, docker registry от jc21 и Shipyard. 

Подключаемся к серверу Shipyard и настраиваем управление.

## Шаг 3. Установка лимитов.
Запускаем контейнер на apache и вводим лимиты.
```
docker run -d -p 8083:80 -m 50m --cpus 0.01 --name httpd:latest
```

## Результат.
Успешно сделаны операции с удалением, установлены Registry, а так-же настроен Shipyard для подключения к API. 

Созданы лимиты для файла.

№№ Вывод
Третья лабораторная работа помогла разобраться в соединениях, освоить команды очистки и создания лимитов.


