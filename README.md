# Docker Deploy Task
## Description
Create three containers. The first container should run an Nginx web server. The second container must deploy a Django project, while the third container must deploy a Laravel project.

### Nginx:
You can build image:
```
docker build -t mynginx
```
Dockerfile:
```
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y net-tools vim nginx

COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```
Nginx configuration:
```
server {
    listen 80;
    server_name 192.168.55.100 localhost;

        location / {
                root   /usr/share/nginx/html;
                index  index.html index.htm;
        }

        location /django/ {
                proxy_pass http://192.168.55.102:8000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }

        location /laravel/ {
                proxy_pass http://192.168.55.103:8000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }
}
```


### Django:
You can build image:
```
docker build -t mydjango .
```
Dockerfile:
```
FROM python:3.9.20-alpine3.20

WORKDIR /app

RUN apk add gcc musl-dev postgresql-dev

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

ENV PATH="/mysite/venv/bin:$PATH"

CMD ["python", "./mysite/djangoProject/manage.py", "runserver", "0.0.0.0:8000"]

```

### Laravel:
You can build image:
```
docker build -t mylaravel
```
Dockerfile:
```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    php \
    php-cli \
    php-mbstring \
    php-xml \
    php-sqlite3 \
    unzip \
    curl \
    git \
    zip

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

WORKDIR /app

RUN composer create-project --prefer-dist laravel/laravel my-laravel-app

COPY ./laravelProject/routes/web.php ./my-laravel-app/routes/

CMD ["php", "./my-laravel-app/artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```

### Docker Compose:
You can use docker-compose:
```
docker-compose up -d
docker-compose down #use this to remove every changes
```
docker-compose file:
```
version: '3.8'
services:
  nginx:
    container_name: mynginx
    image: mynginx:latest
    ports:
      - "80:80"
    networks:
      pnet:
        ipv4_address: 192.168.55.101

  django:
    container_name: mydjango
    image: mydjango:latest
    depends_on:
      - djangodb
    environment:
      DB_NAME: djangodb
      DB_USER: jj
      DB_PASSWORD: mjrude1212
      DB_HOST: djangodb
      DB_PORT: 5432
    networks:
      pnet:
        ipv4_address: 192.168.55.102

  laravel:
    container_name: mylaravel
    image: mylaravel:latest
    environment:
      DB_CONNECTION: mysql
      DB_HOST: laraveldb
      DB_PORT: 3306
      DB_DATABASE: laraveldb
      DB_USERNAME: jj
      DB_PASSWORD: mjrude1212
    networks:
      pnet:
        ipv4_address: 192.168.55.103

  djangodb:
    container_name: djangodb
    image: postgres:latest
    volumes:
      - postgresqldb:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: jj
      POSTGRES_PASSWORD: mjrude1212
      POSTGRES_DB: djangodb
    networks:
      pnet:
       ipv4_address: 192.168.55.104
    expose:
      - '5432'

  laraveldb:
    container_name: laraveldb
    image: mysql:latest
    volumes:
      - mysqldb:/var/lib/mysql
    environment:
      MYSQL_DATABASE: laraveldb
      MYSQL_USER: jj
      MYSQL_PASSWORD: mjrude1212
      MYSQL_ROOT_PASSWORD: mjrude1212
    networks:
      pnet:
        ipv4_address: 192.168.55.105
    expose:
      - '3306'

networks:
  pnet:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.55.0/24
    name: pnet

volumes:
  postgresqldb:
  mysqldb:
```

### Git
You can reach Files in git repo:
```
https://github.com/JavadSafari81/dockerTaskDeploy.git
```
