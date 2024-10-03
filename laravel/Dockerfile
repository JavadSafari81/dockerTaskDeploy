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

