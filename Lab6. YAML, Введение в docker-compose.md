# Отчёт по лабораторной работе №6: YAML, Введение в docker-compose
## Шаг 1. Создание и настройка Docker Compose для Nginx

начали с настройки `docker-compose.yml` файла для запуска контейнера с образом Nginx, который может служить как веб-сервер, так и прокси. Для этого был использован образ `nginx:alpine`. Вначале был прописан базовый конфиг:
```
version: '3'
services:
  # Сервис для Nginx
  nginx:
    build: ./nginx
    ports:
      - 80:80
    depends_on:
      - app
    volumes:
      - ./html:/usr/share/nginx/html
  
  # Сервис для Go-приложения
  app:
    build: .
    ports:
      - 8080
    volumes:
      - .:/src
```

## Шаг 2. Проброс конфигурационного файла и каталога с HTML

Далее пробросили конфигурационный файл и каталог с HTML файлами в контейнер Nginx для настройки веб-сервера и отображения нужного контента.
```
version: '3'
services:
  websrv:
    image: nginx:alpine
    ports:
      - 80:80
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./html:/usr/share/nginx/html

```
Здесь в nginx.conf был настроен сам сервер, а в каталог html были помещены статические файлы для Nginx.
```
```
## Шаг 3. Проброс конфигурационного файла и каталога с HTML
После настройки Nginx мы создали простое приложение на Go. Приложение слушает на порту 8080 и возвращает сообщение при обращении к корневому пути:
```
package main

import (
  "fmt"
  "log"
  "net/http"
  "github.com/gorilla/mux"
)

func main() {
  router := mux.NewRouter()
  router.HandleFunc("/", DoHealthCheck).Methods("GET")
  log.Fatal(http.ListenAndServe(":8080", router))
}

func DoHealthCheck(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Hello, i'm a golang microservice")
  w.WriteHeader(http.StatusAccepted)
}

```
## Шаг 4. Создание Dockerfile для Go приложения
Для сборки Go приложения был создан Dockerfile, который включает все необходимые шаги, такие как установка зависимостей и сборка Go-кода:
```
FROM golang:1.12.7-alpine3.10 AS build
RUN apk --no-cache add gcc g++ make
RUN apk add git
WORKDIR /go/src/app
COPY . .
RUN go get github.com/gorilla/mux
RUN GOOS=linux go build -ldflags="-s -w" -o ./bin/test ./main.go

FROM alpine:3.10
RUN apk --no-cache add ca-certificates
WORKDIR /usr/bin
COPY --from=build /go/src/app/bin /go/bin
EXPOSE 8080
ENTRYPOINT /go/bin/test --port 8080

```
## Шаг 5. Тестирование
Для проверки работы Go приложения мы собрали образ с помощью команды:
```
docker build -t hello_go .
```
Затем запустили контейнер:
```
docker run -itd -p 9999:8080 --name hello_go hello_go
```

## Шаг 6. Создание одного общего docker-compose.
```
version: '3'
services:
  websrv:
    image: nginx:alpine
    ports:
      - 80:80
    volumes:
      - ./html:/usr/share/nginx/html
  app:
    build: .
    volumes:
      - .:/src
    ports:
      - 8080

```
## Шаг 7. Настройка Nginx 
Обновляем конфиг Nginx
```
events {
    worker_connections 1024;
}

http {
  server_tokens off;
  server {
    listen 80;
    root  /var/www;

    location / {
      index index.html;
    }

    location /api/ {
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_set_header Host            $http_host;
      proxy_pass http://app:8080/;
    }
  }
}
```
Добавляем контейнер в docker-compose
```
version: '3'
services:
  app:
    build: .
    ports:
      - 8080
  nginx:
    build: ./nginx
    ports:
      - 80:80
    depends_on:
      - app
```
## Шаг 8. Сборка и запуск.
```
docker-compose build
docker-compose up
```
## Заключение
В результате проделанных шагов мы создали и настроили многоконтейнерное приложение, использующее Go приложение и веб-сервер Nginx. Все сервисы были интегрированы через Docker Compose, что позволило легко управлять и запускать оба контейнера одновременно.